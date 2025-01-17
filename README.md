`docker-pull-request-script`

````hcl
provider "aws" {
  region = "us-east-1" # Change to your desired region
}

resource "aws_instance" "example" {
  count         = 10                      # Number of instances to create
  ami           = "ami-03972092c42e8c0ca" # Replace with your AMI ID
  instance_type = "t2.micro"              # Change to your desired instance type

  user_data = <<-EOF
    #!/bin/bash
    # Update the package repository and install Docker
    yum update -y
    yum install -y docker

    # Start Docker service
    systemctl start docker
    systemctl enable docker

    # Pull from Docker Hub multiple times with a random delay
    for i in {1..1000}
    do
        docker pull iamtejas23/e-kart
        if [ $? -ne 0 ]; then
            echo "Error on attempt $i, exiting..."
            exit 1
        fi
        docker rmi iamtejas23/e-kart
        if [ $? -ne 0 ]; then
            echo "Error on removing image on attempt $i, exiting..."
            exit 1
        fi
        sleep $((RANDOM % 10 + 1)) # Random sleep between 1 and 10 seconds
    done
  EOF

  tags = {
    Name = "ExampleInstance-${count.index}"
  }
}
```

This version maintains proper indentation and alignment, ensuring that the Terraform configuration is clean and readable.
