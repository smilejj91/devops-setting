# gitlab-setting

### How to setup gitlab
```bash
# pre-cond : install docker

$ docker compose up -d

# initial password

$ docker exec -it {gitlab container name} grep 'Password:' /etc/gitlab/initial_root_password
```
