# Root Cause Analysis

Fill this in as you go. Bullet points are fine — be specific, not long.

---

## Defect 1: Proxy image fails to build

### Symptom / error output
The proxy image failed during the nginx installation.
The log showed a dpkg prompt for "/etc/nginx/nginx.conf" and then failed with "end of file on stdin at conffile prompt".

### How I diagnosed it
I ran "./scripts/run-tests.sh" and read the full build error.
I noticed that the custom "nginx.conf" was copied before nginx was installed.

### Root cause
nginx tried to install its own config file, but a custom one already existed in the same path.

### Fix
I installed nginx first and copied the custom "nginx.conf" afterwards.

---

## Defect 2: Proxy cannot reach the backend

### Symptom / error output
Requests through the proxy returned a 502 Bad gateway.
The nginx log showed it was trying to connect to "127.0.0.1:8080".

### How I diagnosed it
I checked the proxy logs after the build issue was fixed.
I saw that nginx was using localhost to reach the backend.

### Root cause
Inside the proxy container, "127.0.0.1:8080" pointed to the proxy itself, not to the backend container.

### Fix
I changed the upstream address from the original to "backend:8080".

---

## Defect 3: Tests still fail after fixing defect 2

### Symptom / error output
The proxy was pointing to the correct backend address, but requests still failed with "Connection refused"

### How I diagnosed it
I checked the logs and saw that the backend needed a few seconds before it was ready to accept requests.

### Root cause
The containers started in the right order, but the tests started before the backend was actually ready.

### Fix, and why I chose it over the alternatives
I added a healthcheck to the backend and made the proxy wait until the backend was healthy.
I chose this instead of adding a fixed sleep because the startup time can vary, while a healthcheck waits only as long as needed.

---

## Defect 4: CI pipeline fails

### Symptom / error output
GitHub Actions failed with "Permission denied" when trying to run "./scripts/run-tests.sh".

### How I diagnosed it
I opened the failed CI run and checked the "Execute test suite" step.
The error showed that the script existed, but GitHub could not execute it.

### Root cause
The test script was not committed with executable permission.

### Fix
I marked "scripts/run-tests.sh" as executable in Git and pushed the change.

---

## Bonus questions

Optional. Answer only the ones you got to — number them, skip the rest.

## Bonus 1: Reduced proxy image size
I changed the proxy base image from Ubuntu to "nginx:alpine".
I also removed packages that were not needed at runtime.
The image size went from about 121MB to 25.9MB.
The tests still passed after the change.

---
## Bonus 2: Run proxy as non-root
I switched the proxy to the unprivileged nginx image.
I changed nginx to listen on port "8080" inside the container.
I also moved the nginx PID file to "/tmp/nginx.pid" so the non-root user could write it.
I verified the container was running as user "nginx" and all tests still passed.

---

## Anything you would do differently with more time
I would spend more time on additional security improvements for the proxy.
