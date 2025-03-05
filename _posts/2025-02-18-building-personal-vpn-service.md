---
layout: post
title: "Building a Personal VPN Service with AWS, Terraform, and iOS"
date: 2025-02-18 12:00:00 +0800
categories: [projects, cloud, mobile]
tags: [aws, terraform, ios, swiftui, vpn, serverless]
image: /assets/images/vpn-architecture.png
excerpt: "While on sabbatical in Taipei, Taiwan, I embarked on a project to create my own VPN service using AWS. The goal was to build a complete system that would allow me to appear in a different location while traveling, with the ability to easily control the VPN from my iPhone."
---
![Clouds rolling over the hills in Xiding, TW](/assets/images/xiding-clouds.gif)
While on sabbatical in Taipei, Taiwan, I embarked on a project to create my own VPN service using AWS. The goal was to build a complete system that would allow me to appear in a different location while traveling, with the ability to easily control the VPN from my iPhone. What made this project particularly interesting was that nearly all of the code was generated using AI tools like ChatGPT and Claude.

## Project Overview

The project consists of three main components:
1. A VPN server running on AWS EC2
2. A serverless REST API for controlling the VPN instance
3. An iOS app for managing the VPN service

Let's dive into each component and see how they were built.

## Setting Up the VPN Infrastructure

The VPN infrastructure is defined using Terraform, which allows for infrastructure as code (IaC). The configuration creates:
- A VPC with a public subnet
- Security groups for VPN traffic
- An EC2 instance running OpenVPN
- An Elastic IP for consistent access

Here's the core of the Terraform configuration:

```hcl
resource "aws_instance" "vpn_instance" {
  ami                    = "ami-0b16505c55f9802f9" # Amazon Linux 2
  instance_type          = "t4g.micro"
  subnet_id              = aws_subnet.vpn_subnet.id
  vpc_security_group_ids = [aws_security_group.vpn_sg.id]
  key_name              = var.ssh_key_name

  user_data = <<-EOF
              #!/bin/bash
              yum update -y
              amazon-linux-extras enable epel
              yum install -y openvpn easy-rsa
              curl -O https://raw.githubusercontent.com/Angristan/OpenVPN-install/master/openvpn-install.sh
              chmod +x openvpn-install.sh
              AUTO_INSTALL=y ./openvpn-install.sh
              EOF

  tags = {
    Name = "VPN-Instance"
  }
}
```

![AWS Console showing the running EC2 instance with VPN](/assets/images/vpn-ec2-console.png)

## Building the REST API

To control the VPN instance, I created a serverless REST API using AWS API Gateway and Lambda. The API provides three main functions:
- Start the VPN instance
- Stop the VPN instance
- Check the VPN status

The Lambda function handling these operations is written in Python:

```python
def lambda_handler(event, context):
    action = ""
    if "queryStringParameters" in event and event["queryStringParameters"] is not None:
        action = event["queryStringParameters"].get("action", "").lower()
    elif "action" in event and event["action"] is not None:
        action = event.get("action", "").lower()
    
    if action not in ["start", "stop", "status"]:
        return {
            "statusCode": 400,
            "body": json.dumps({"message": "Invalid action. Use 'start', 'stop', or 'status'."})
        }

    try:
        if action == "start":
            ec2.start_instances(InstanceIds=[INSTANCE_ID])
            message = f"Instance {INSTANCE_ID} is starting."
        # ... rest of the handler code
```

The API is secured using API keys and includes CORS support for the iOS app.

![API Gateway console showing the API endpoints](/assets/images/vpn-api-gateway.png)

## Developing the iOS App

The iOS app was built using SwiftUI and follows the MVVM (Model-View-ViewModel) architecture. It provides a clean interface for managing the VPN service with features like:
- Real-time status monitoring
- Start/Stop controls
- Secure API key storage
- Status history

Here's a look at the main view model that manages the VPN state:

```swift
@MainActor
class VPNViewModel: ObservableObject {
    @Published var vpnState: VPNState = .stopped
    @Published var isLoading = false
    @Published var statusMessage = "Checking status..."
    
    func toggleVPN() async {
        isLoading = true
        do {
            let action: VPNAction = vpnState == .stopped ? .start : .stop
            let response = try await vpnClient.controlVPN(action: action)
            // ... handle response
        } catch {
            errorMessage = error.localizedDescription
        }
        isLoading = false
    }
}
```

The app uses modern iOS features like:
- Async/await for network calls
- Secure storage in the Keychain
- SwiftUI for the user interface
- Environment objects for settings management

![Animated GIF of VPN Control app running](/assets/images/Simulator Screen Recording - iPhone 16 Pro Max - 2025-03-05 at 20.13.14.gif)


## Using AI for Development

One of the most interesting aspects of this project was the extensive use of AI tools for code generation. Here's how AI assisted in each component:

1. Infrastructure:
   - Generated the Terraform configuration
   - Helped with security group rules
   - Created the VPN installation script

2. API Development:
   - Generated the Lambda function code
   - Created the API Gateway configuration
   - Helped with CORS settings and security

3. iOS Development:
   - Generated the SwiftUI views
   - Created the MVVM architecture
   - Implemented the networking layer
   - Added secure storage for the API key

The AI tools were particularly effective at:
- Generating boilerplate code
- Implementing best practices
- Handling error cases
- Creating documentation

## Lessons Learned

Throughout this project, several key insights emerged:

1. AI tools are incredibly effective for scaffolding projects but require human oversight for:
   - Security considerations
   - Architecture decisions
   - Integration between components

2. AWS serverless architecture provides a cost-effective way to manage infrastructure

3. SwiftUI and MVVM create a maintainable and responsive mobile application

4. Infrastructure as Code (Terraform) makes the system reproducible and maintainable

## Future Improvements

Some potential enhancements for the project:

1. Add support for multiple VPN locations
2. Implement automatic shutdown when idle to save costs
3. Add VPN connection metrics and usage statistics
4. Implement push notifications for status changes
5. Add support for other VPN protocols

## Conclusion

This project demonstrates how modern cloud services, mobile development, and AI tools can come together to create a practical solution. The combination of AWS services, Terraform, and iOS development provides a flexible and secure way to manage a personal VPN service, while AI tools significantly accelerated the development process.

![System Architecture Diagram](/assets/images/vpn-architecture.png)

The complete source code, GenAI chat transcripts, and documentation for this project are available on GitHub at [github.com/TerrorTunnels](https://github.com/TerrorTunnels).
