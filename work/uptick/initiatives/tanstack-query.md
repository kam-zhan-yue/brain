Steps to accomplish
- Map out the current flow
- Get an isolated test to work
- Research into tanstack query layout
- Write script

## Current Flow

```
# 1. Calls an mcommand
python manage.py sync_frontend

# 2. Calls even more commands
call_command("generate_frontend_use_query", **options)
call_command("generate_typed_actions", **options)

# 3. Generate the schema based on the typed action
schema_file = output_json_schema(typed_action.request_schema)
// This creates a request-schema.json

# 4. Creates a type based on the schema
json2ts(schema_file)
// This creates a request-type.ts file with the request type
export interface AddRemarksToRectification {
  remark_ids?: string[]
  [k: string]: unknown
}

# 5. Do the same for the response schema, if there is one.
schema_file = output_json_schema(typed_action.response_schema)
json2ts(schema_file)

# 6. Obsoleted files are deleted

# 7. Typed Action Schemas are generated from each file
- Load Request Schema: load_schema(sub_dir, "request-schema.json")
- Load Response Schema: load_schema(sub_dir, "response-schema.json")
- Generate Hook: generate_hook_content(request_schema, response_schema)

# 8. Generate using a template
return templating_engine.get_template("hook.ts.txt").render(...)
```

OK STEPS HAVE CHANGED
- Write an example typed action of how it can work
- Write the generation script

## Tanstack Query

Instead of using generateUseLazyApiFetch, we do it ourselves. However, it depends on GET, POST, etc.

In terms of Tanstack Query for Entities, all we had to do was:
- Specify a definition for the hook
- Return the entity query

In terms of Tanstack Query for Typed Actions, we need to:
- Specify the definition type for the request AND the payload, if there is one
- Return the query
- If it is a get query, we need to return useQuery
- If it is a patch, post, put, we need to return useMutation

## Current Typed Action Flow

```
# 1. generateUseApiFetch
- This generates fetch options
- Also populates the URL if there is a param or not

# 2. useApiFetch
- Makes lazyFetchOptions (skips or not)
- Layer for refetching

# 3. useLazyApiFetch (rxjs)
- Calls the onError and onSuccess
- Separates out error and success results
- LoadingResult only has status, no data

# 4. fetch (line 84)
- calls resultFromFetch
- waits for async result

# 5. resultFromFetch
- Calls a validation on the input schema
- Separates out TypeErrors
- Calls a validation on the output schema
```

Our goal is to make functions that can effectively replace these layers with a more asynchronous function call, while also still using the same data and error transformation functions.

## Revamped Flow
1. Make the request and store in response
2. Transform the response the same way it is done in useLazyApiFetch