[See documentation here](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/)

Images allow containers to pull files and configurations from the web. It is a standardised package that includes all the files, libraries, and configurations to run a container.

For a PostgreSQL image, that image will package the database binaries, config files, and other dependencies. There are two principles of images:
1. Images are immutable. Once an image is created, it can't be modified. You can only make a new image or add changes on top of it.
2. Container images are composed of layers. Each layer represents a set of file system changes that add, remove, or modify files.