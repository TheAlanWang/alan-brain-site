1. login

```bash
aws ecr get-login-password --region us-west-1 \
| docker login --username AWS --password-stdin 6xxxxx54.dkr.ecr.us-west-1.amazonaws.com
```

2. push
```bash
docker push 646xxx1354.dkr.ecr.us-west-1.amazonaws.com/cs6650/assignment1:latest
```
