# jenkins-demo-app — Jenkins CI/CD pipeline

A small Node.js app built, tested and deployed by a Jenkins pipeline using Docker.

## Tools
Jenkins, Docker, GitHub, Node.js

## Files
| File | Purpose |
|------|---------|
| `Jenkinsfile` | Declarative pipeline: Checkout, Build, Test, Deploy |
| `Dockerfile` | Builds the app image |
| `Dockerfile.jenkins` | Jenkins image that includes the Docker CLI |
| `app.js`, `server.js`, `app.test.js` | App and tests |

## Pipeline stages
1. **Checkout** - pulls the code from GitHub
2. **Build** - `docker build` the image, tagged with the build number and `latest`
3. **Test** - runs `node --test` inside the built image
4. **Deploy** - replaces the running container and checks `/health`

Trigger: `pollSCM` checks GitHub every minute and starts a build on each new commit.

## Run Jenkins
```bash
docker build -t my-jenkins -f Dockerfile.jenkins .
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --user root my-jenkins
```
Open http://localhost:8080, then create a Pipeline job pointing at this repo (Pipeline script from SCM, branch `*/main`, script path `Jenkinsfile`).

## Result
The app is served at http://localhost:3000 after a successful build.

## Screenshots
See the `screenshots/` folder.

![Jenkins build success](screenshots/jenkins-build-success.png)
![Health endpoint](screenshots/app-health.png)
![App running](screenshots/app-running.png)
