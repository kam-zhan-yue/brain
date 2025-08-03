## Changing Materials Properties at Runtime
- Get the material from the appropriate renderer
```csharp
private void Awake() {
	Material mat = GetComponent<Renderer>().material
	mat.SetPropertyByID("SomeProperty", 1f);
}
```
- Set the material property on that, instead of `sharedMaterial`