# Automated CI/CD Pipeline for a Flask Web App using Jenkins, Docker, and Ansible

This project is prepared for demonstration of a complete DevOps pipeline using Jenkins, Docker, and Ansible.
It is based on the provided CMS Flask project and cleaned for clarity. Nginx folder/configs were removed as requested.

## Contents
- app/                         : Flask application (app.py, templates, static, requirements.txt, Dockerfile)
- Jenkinsfile                  : Jenkins pipeline (build, push, deploy)
- docker-compose.yml           : (if present) original compose file kept
- ansible/                     : Ansible inventory and deployment playbook
- README.md                    : This file

## Quick Local Run (no Docker)
1. Create virtual environment and activate
   - Windows: `python -m venv venv` & `venv\Scripts\activate`
   - Linux/Mac: `python3 -m venv venv` & `source venv/bin/activate`
2. Install requirements: `pip install -r app/requirements.txt`
3. Run app: `python app/app.py`
4. Open `http://localhost:5000`

## Quick Docker Build (local test)
1. Build image:
   `docker build -t yourdockerhubusername/flask-cicd:latest -f app/Dockerfile .`
2. Run container:
   `docker run -p 5000:5000 yourdockerhubusername/flask-cicd:latest`

## Ansible Deploy
1. Update `ansible/inventory.ini` with your EC2 IP and key path.
2. Run:
   `ansible-playbook -i ansible/inventory.ini ansible/deploy.yml --extra-vars "docker_image=yourdockerhubusername/flask-cicd:latest"`

## Notes
- Replace placeholder values (dockerhub username, EC2 IP) before production use.
- Jenkins requires Docker CLI access and credentials to push images.
