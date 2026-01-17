[See documentation here](https://docs.godotengine.org/en/stable/tutorials/shaders/compute_shaders.html#doc-compute-shaders)

A compute shader is a special type of shader program that is oriented towards general purpose programming. They are more flexible than vertex shaders and fragment shaders as they don't have a fixed purpose.

Unlike fragment shaders and vertex shaders, compute shaders have very little going on behind the scenes. The code you write is what the GPU runs and very little else. This can make them a very useful tool to offload heavy calculations to the GPU.

### An Example

```glsl
#[compute]
#version 450

// invocations in the (x, y, z) dimensions
layout (local_size_x = 2, local_size_y = 1, local_size_z = 1) in;

// A binding to the buffer as we create in our script
layout (set = 0, binding = 0, std430) restrict buffer MyDataBuffer {
  float data[];
} my_data_buffer;

void main() {
  // gl_GlobalInvocationID.x uniquely identifies this invocation across all work groups
  my_data_buffer.data[gl_GlobalInvocationID.x] *= 2.0;
}
```

This code takes an array of floats, multiplies each element by 2 and stores the result back in the buffer array.

```glsl
#[compute]
#version 450
```
- Compute is a Godot-specific hint needed for the editor to import the shader file
- The code is using GLSL version 450

```glsl
layout (local_size_x = 2, local_size_y = 1, local_size_z = 1) in;
```
Next, we communicate the number of invocations to be used in each workgroup. 
- Invocations are instances of the shader that are running within the same workgroup.
- When we launch a compute shader from the CPU, we tell it how many workgroups to run.
- Workgroups run in parallel to each other. 
- While running one workgroup, you cannot access information in another workgroup.
- Invocations in the same workgroup can store limited access to other invocations

Think about workgroups and invocations as a giant nested `for` loop:

```c++
for (int x = 0; x < workgroup_size_x; x++) {
  for (int y = 0; y < workgroup_size_y; y++) {
     for (int z = 0; z < workgroup_size_z; z++) {
        // Each workgroup runs independently and in parallel.
        for (int local_x = 0; local_x < invocation_size_x; local_x++) {
           for (int local_y = 0; local_y < invocation_size_y; local_y++) {
              for (int local_z = 0; local_z < invocation_size_z; local_z++) {
                 // Compute shader runs here.
              }
           }
        }
     }
  }
}
```

```c++
layout (set = 0, binding = 0, std430) restrict buffer MyDataBuffer {
  float data[];
} my_data_buffer;
```

Here, we provide information about the memory that the compute shader will have access to. The `layout` property allows us to tell the shader where to look for the buffer. We will need to match these `set` and `binding` positions from the CPU side later.

The `restrict` keyword tells the shader that this buffer is only going to be accessed from one place in this shader. In other words, we won't bind this buffer in another `set` or `binding` index. This is important as it allows the shader to compiler to optimise the shader code.

> Always use `restrict` when you can

This is an *unsized* buffer, which means it can be any size. So, we need to be careful not to read form an index larger than the size of the buffer.

```c++
void main() {
  // gl_GlobalInvocationID.x uniquely identifies this invocation across all work groups
  my_data_buffer.data[gl_GlobalInvocationID.x] *= 2.0;
}
```

Finally, we write the `main` function which is where all the logic happens. We access a position in the storage buffer using the `gl_GlobalInvocationID` built-in variables. This gives the global unique ID for the current information.

## Creating a Local RenderingDevice
To interact with and execute a compute shader, we need a script. 

We need to create a local `RenderingDevice`

```c++
var rd := RenderingServer.create_local_rendering_device()
```

Then, we can load the newly created shader file `compute_shader.glsl` and create a precompiled version using:

```c++
var shader_file := load("res://shaders/compute_shader.glsl")
var shader_spirv: RDShaderSpirv = shader_file.get_spirv()
var shader := rd.shader_create_from_spirv(shader_spirv)
```

## Provide Input Data

We need to create a buffer to pass values to a compute shader. We are dealing with an array of floats, so we will use a storage buffer. A storage buffer takes an array of bytes and allows the CPU to transfer data to and from the GPU.

```c++
# Prepare the data
var input := PackedFloat32Array([1, 2 3, 4, 5, 6, 7, 8, 9, 10])
var input_bytes := input.to_byte_array()

# Create a storage buffer to hold the float values
# Each float has 4 bytes (32 bit) so 10 x 4 = 40 bytes
var buffer := rd.storage_buffer_create(input_bytes.size(), input_bytes)
```

With the buffer in place, we need to tell the rendering device to use the buffer. To do that, we create a uniform (like in normal shaders) and assign it to a uniform set which we can pass to our shader.

```python
var uniform := RDUniform.new()
uniform.uniform_type = RenderingDevice.UNIFORM_TYPE_STORAGE_BUFFER
uniform.binding = 0 # this needs to match the "binding" in the shader file
uniform.add_id(buffer)
var uniform_set := rd.uniform_set_create([uniform], shader, 0)
```

## Defining a Compute Pipeline

The next step is to create a set of instructions the GPU can execute. We need a pipeline and a compute list. The steps we need to do to compute our result are:
1. Create a new pipeline
2. Begin a list of instructions for the GPU to execute
3. Bind our compute list to the pipeline
4. Bind our buffer uniform to the pipeline
5. Specify how many workgroups to use
6. End the list of instructions

```python
var pipeline := rd.compute_pipeline_create(shader)
var compute_list := rd.compute_list_begin()
rd.compute_list_bind_compute_pipeline(compute_list, pipeline)
rd.compute_list_bind_uniform_set(compute_list, uniform_set, 0)
rd.compute_list_dispatch(compute_list, 5, 1, 1)
rd.compute_list_end()
```

Note that we are dispatching the compute shader with 5 work groups in the X axis, and one in the others. Since we have 2 local invocations in the X axis (specified in the shader), 10 compute shader invocations will be launched in total. If you want to read or write to indices outside of the range of your buffer, you may access memory outside of your shaders control or parts of other variables which may cause issues on some hardware.

## Executing a Compute Shader

So far, we have recorded what we would like the GPU to do, but we have not actually run the shader program. To execute our shader program, we need to submit the pipeline to the GPU and wait for the execution to finish.

```python
rd.submit()
rd.sync()
```

Ideally, you would not call `sync()` to synchronise the `RenderingDevice` right away as it will cause the CPU to wait for the GPU to finish working. In general, you want to wait *at least* 2 or 3 frames before synchronising so that the GPU is able to run in parallel with the CPU.

## Retrieving Results
In the shader, we modified the storage buffer. In other words, the shader read from our array and stored the data in the same array again so that our results are already there.

All we need to do is then retrieve the data and print out the results.

```python
# Retrieve the results
var output_bytes := rd.buffer_get_data(buffer)
var output := output_bytes.to_float32_array()

print("Input: ", input)
print("Output: ", output)
```

## Freeing Memory
The `buffer`, `pipeline`, and `uniform_set` variables are each an RID. Because `RenderingDevice` is meant to be a lower-level API, RIDs aren't freed automatically. This means that once you're done using `buffer` or any other RID< you are responsible for freeing its memory manually using the `RenderingDevice`'s `free_rid()` method.