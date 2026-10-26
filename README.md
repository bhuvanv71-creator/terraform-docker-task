# Task 3: Infrastructure as Code with Terraform

Provisioning a local Docker container (nginx) using Terraform and the Docker provider.

## Objective
Provision a local Docker container using Terraform.

## Tools
- Terraform
- Docker Desktop (Windows)
- PowerShell

## What I Did
1. Installed Terraform and verified Docker Desktop was running.
2. Wrote `main.tf` using the `kreuzwerker/docker` provider to pull `nginx:latest` and run it as a container mapped to host port 8081.
3. Ran `terraform init` to download the provider.
4. Ran `terraform plan` to preview the changes.
5. Ran `terraform apply` to create the image and container.
6. Verified with `docker ps`, `http://localhost:8081`, and `terraform state list` / `terraform state show`.
7. Ran `terraform destroy` to remove all resources.

## Files
| File | Purpose |
|------|---------|
| `main.tf` | Terraform config (provider, image, container) |
| `.terraform.lock.hcl` | Provider version lock |
| `logs-*.txt` | Execution logs for init, plan, apply, state, destroy |
| `screenshots/` | Proof of execution |

## Commands Used

    terraform init
    terraform plan
    terraform apply -auto-approve
    terraform state list
    terraform state show docker_container.nginx
    terraform destroy -auto-approve

## Debugging Note
The first `terraform apply` failed because port 8080 was already allocated on my machine. I changed the host port to 8081 in `main.tf`, removed the half-created container, and re-ran plan and apply successfully.

## Key Concepts
- **Implicit dependency:** the container references `docker_image.nginx.image_id`, so Terraform creates the image first.
- **State file:** maps the config to real resources. It is excluded from Git via `.gitignore`.
- **Plan vs apply:** plan previews changes, apply executes them.

## Interview Questions

1. **What is IaC?** Managing infrastructure through versioned code instead of manual configuration.
2. **How does Terraform work?** It reads `.tf` files, builds a dependency graph, compares desired state with the state file, and calls provider APIs to reconcile.
3. **What is the state file?** `terraform.tfstate` maps config to real resources and is used to compute diffs. Teams store it remotely (e.g. S3 with locking).
4. **Apply vs plan?** `plan` previews changes without making them; `apply` executes them.
5. **What are providers?** Plugins that let Terraform talk to platforms such as AWS, Docker, and Azure.
6. **What is resource dependency?** The ordering between resources, either implicit through references or explicit through `depends_on`.
7. **How do you handle secret variables?** Use `sensitive = true`, `TF_VAR_*` environment variables, gitignored `.tfvars`, or a secrets manager. Never hardcode secrets, and protect the state file since it can contain them.
8. **Benefits of Terraform?** Repeatability, version control, automation, drift detection, multi-cloud support, reviewable changes, faster provisioning.

## Outcome
Understood the full IaC lifecycle: init, plan, apply, inspect state, destroy.



## Screenshots

### Terraform apply
![Apply](screenshots/01-terraform-apply.jpeg)

### Container running (docker ps + curl)
![docker ps](screenshots/02-docker-ps.jpeg)

### nginx in browser
![Browser](screenshots/03-browser.jpeg)

### Terraform state
![State](screenshots/04-state-list.jpeg)

### Terraform destroy
![Destroy](screenshots/05-terraform-destroy.jpeg)

### Port 8080 conflict (Jenkins)
![Port conflict](screenshots/06-port-conflict.jpeg)
