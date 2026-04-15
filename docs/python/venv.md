`venv` allows you to manage separate package installations for different projects. It creates a "virtual" isolated Python installation. When you switch projects, you can create a new virtual environment which is isolated from other virtual environments.

`venv` basically enables you to have different packages for different projects, just like `mise`.

You can create a new virtual environment by running the following command. It will create a new virtual environment in a local folder called `.venv`

```bash
python3 -m venv .venv
```

## Activating a Virtual Environment
Before you can start installing or using packages in your virtual environment, you need to activate it. Activating a virtual environment will put the virtual environment specific `python` and `pip` executables into your shell's `PATH`.

```bash
source .venv/bin/activate
```

To confirm the virtual environment is activated, check the location of your Python interpretator.

```bash
which python
```

While the virtual environment is active, the above command will output a filepath that includes the `.venv` directory.