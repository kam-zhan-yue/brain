Imagine you are building a web app that has three main components: a React frontend, a Python API, and a PostgreSQL database. If you wanted to work on the project, you'd have to install Node, Python, and PostgreSQL.

Containers are isolated processes for each of your app's components. Each component runs in its own isolated environment, completely isolated from everything else on your machine. Containers are:
- Self-contained: Each container has everything it needs to function with no reliance on any pre-installed dependencies on the host machine
- Isolated: Since containers run in isolation, they have minimal influence on the host and on other containers, increasing the security of your applications
- Independent: Each container is independently managed. Deleting one container won't affect any others.
- Portable: Containers can run anywhere. The container that runs on your development machine will work the same way in a data centre or anywhere in the cloud.

## Virtual Machines (VMs)
A VM is an entire operating system with its own kernel, hardware drivers, programs, and applications. Spinning up a VM to isolate a single application is a lot of overhead. A container is simply an isolated process with all of the files it needs to run. If you run multiple containers, they share the same kernel, allowing you run more applications on less infrastructure.

> Quite often, you will see containers and VMs used together. As an example, in a cloud environment, the provisioned machines are typically VMs. However, instead of provisioning one machine to run one application, a VM with a container runtime can run multiple containerised applications, increasing resource utilisation and reducing costs. 