1. docker image build -t psweb .
2. docker container run -d --name web1 -p 8080:8080 psweb
3. <browse> http://localhost:8080/