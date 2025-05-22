# Node.js MySQL Application with AWS EC2 and RDS

A Docker-based Node.js application deployed on AWS EC2 that connects to an AWS RDS MySQL database.

## Architecture Overview

```
EC2 Instance (Docker Node-App) ←→ RDS (MySQL Database)
```

## Prerequisites

- AWS Account with appropriate permissions
- Basic knowledge of AWS EC2 and RDS services
- Docker fundamentals
- Node.js and MySQL understanding

## AWS Infrastructure Setup

### 1. RDS Instance Configuration

#### Create RDS MySQL Instance
- **Tier**: Use Free Tier eligible option
- **Engine**: MySQL
- **Username**: `admin` (default)
- **Password**: Set a secure password (avoid special characters for compatibility)
- **Public Access**: Enable "True" to allow connections from local/remote servers
- **Security Group**: Create and configure to allow port 3306 from everywhere (0.0.0.0/0)
- **Endpoint**: Note the endpoint hostname after creation for database connection

#### Security Group Settings
```
Type: MySQL/Aurora
Protocol: TCP
Port: 3306
Source: 0.0.0.0/0 (or specify your IP range for better security)
```

### 2. EC2 Instance Setup

#### Install Docker
```bash
sudo yum install -y docker
sudo service docker start
sudo usermod -aG docker ec2-user
```

#### Pull the Docker Image
```bash
sudo docker pull philippaul/node-mysql-app:02
```

## Application Deployment

### 1. Run the Application Container

```bash
docker run --rm -p 80:3000 \
-e DB_HOST="your-rds-endpoint-hostname" \
-e DB_USER="admin" \
-e DB_PASSWORD="your-database-password" \
-d philippaul/node-mysql-app:02
```

**Environment Variables:**
- `DB_HOST`: Your RDS instance endpoint
- `DB_USER`: Database username (default: admin)
- `DB_PASSWORD`: Your RDS instance password
- Port mapping: `80:3000` (host:container)

### 2. Database Connection Testing

Connect to your MySQL database directly for testing:

```bash
docker run -it --rm mysql:8.0 mysql -h your-rds-endpoint.com -u admin -p
```

## Configuration Details

### Port Configuration
- **Application Port**: 3000 (internal container port)
- **Exposed Port**: 80 (EC2 instance port)
- **Database Port**: 3306 (RDS MySQL port)

### Environment Variables
The application expects the following environment variables:
- `DB_HOST`: RDS endpoint hostname
- `DB_USER`: MySQL username
- `DB_PASSWORD`: MySQL password

## Security Considerations

### Production Recommendations
1. **Database Security**: 
   - Restrict RDS security group to only allow EC2 security group access
   - Use VPC for internal communication
   - Enable encryption at rest and in transit

2. **EC2 Security**:
   - Configure EC2 security group to allow only necessary ports
   - Use IAM roles instead of hardcoded credentials where possible
   - Keep the instance updated

3. **Application Security**:
   - Store sensitive environment variables securely
   - Use AWS Secrets Manager for database credentials
   - Implement proper logging and monitoring

## Troubleshooting

### Common Issues

1. **Connection Refused**:
   - Check RDS security group allows port 3306
   - Verify RDS public accessibility is enabled
   - Confirm endpoint hostname is correct

2. **Docker Permission Issues**:
   - Ensure user is added to docker group
   - Restart session after adding to docker group

3. **Application Not Accessible**:
   - Check EC2 security group allows HTTP traffic (port 80)
   - Verify container is running: `docker ps`
   - Check container logs: `docker logs <container-id>`

### Useful Commands

```bash
# Check running containers
docker ps

# View container logs
docker logs <container-name-or-id>

# Stop container
docker stop <container-name-or-id>

# Remove container
docker rm <container-name-or-id>

# Connect to running container
docker exec -it <container-name-or-id> /bin/bash
```

## Project Structure

```
project/
├── README.md
├── Dockerfile
├── package.json
├── src/
│   ├── app.js
│   ├── routes/
│   └── config/
└── docker-compose.yml (optional)
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For issues and questions:
- Check the troubleshooting section above
- Review AWS documentation for EC2 and RDS
- Consult Docker documentation for container issues

---

**Note**: Replace placeholder values (your-rds-endpoint-hostname, your-database-password) with your actual configuration values.
