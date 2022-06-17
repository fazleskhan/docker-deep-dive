
Linux images can not be created on Windows Server 2019 because it does not support WSL2.
Instead Linux images need to be created on Windows 10 because the OS does support WSL2.
Then the image is push up to the image repo (DockerHub.com).
Finally, the image is fetch from repo and a container is created on the Windows Server 2019 host.

1. <Windows 10> docker image build -t fazleskhan/psweb https://github.com/fazleskhan/docker-deep-dive.git
2. <Windows 10> docker container run -d --name web1 -p 8080:8080 fazleskhan/psweb
3. <Windows 10> <browse> http://localhost:8080/
4. <Windows 10> docker image push fazleskhan/psweb
5. <Windows 10> docker history fazleskhan/psweb
6. <Windows 10> docker image inspect fazleskhan/psweb
7. <Windows Server 2019> docker container run -d --name web1 -p 8080:8080 fazleskhan/psweb
8. <Windows Server 2019> <browse> http://localhost:8080/

https://hub.docker.com/repository/docker/fazleskhan/psweb
https://github.com/fazleskhan/docker-deep-dive