# Grafana-Loki-EC2
Grafana-Loki-EC2
Instructions for Usage:


Launch the Instance:

Go to the AWS EC2 Console.
During the "Configure Instance" step, paste this script into the User Data field.
Verify After Launch:

SSH into the instance and check that Grafana and Loki are running:
Grafana: Visit http://<instance-IP>:3000
Loki: Check its logs using:
bash
Copy code
sudo systemctl status loki

Ec2 recomendations 
t3.medium or larger with 50-100gb EBS or s3 for logs.
