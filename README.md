Build, Deploy and Monitor on Kubernetes

Summary
Build a Docker image from a "hello world" web application, deploy it to a Kubernetes cluster and instrument monitoring.

Setup
Some requirements are left intentionally vague:
There must be a "hello world" type web application in your personal Github account. The language doesn't matter, e.g. ruby, python, java, etc. 
SCHO using "Kubernetes Wordsmith Demo" to complete the setup portion of the exercise
You may run a Docker registry and Kubernetes on your localhost (see Docker Desktop).

There must be Docker image(s) built from a Dockerfile in the application source code.
SCHO https://github.com/dockersamples/k8s-wordsmith-demo/blob/master/db/Dockerfile for the db image
SCHO https://github.com/dockersamples/k8s-wordsmith-demo/blob/master/web/Dockerfile for the web image
SCHO https://github.com/dockersamples/k8s-wordsmith-demo/blob/master/words/Dockerfile for the word image including mvn install
SCHO installed Docker Desktop for Mac and enabled the kubernetes(v1.19.7) cluster 

There must be Kubernetes deployment, service and ingress definitions used to start the application with reference to the aforementioned Docker image(s).
SCHO https://github.com/dockersamples/k8s-wordsmith-demo/blob/master/kube-deployment.yml

There must be CPU and RAM monitoring for the Kubernetes PODs.

Requirements
I must be able to view your application code, Dockerfile, Kubernetes manifests and monitoring configuration.

You must be able to deploy the application using kubectl commands
SCHO -Deploy Using a Kubernetes Manifest
kubectl apply -f kube-deployment.yml 

You must be able to view application deployment status using kubectl commands
SCHO From my Mac terminal, %  kubectl get svc 
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
db    3       ClusterIP      None            <none>        5432/TCP         8h
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          33h
web          LoadBalancer   10.107.22.117   localhost     8081:32557/TCP   8h
words        ClusterIP      None            <none>        8080/TCP         8h
 
% kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
db-68df76f5d6-mnpk2      1/1     Running   0          8h
web-6f496699bd-9rdxx     1/1     Running   0          8h
words-84bccbc5d7-2h56x   1/1     Running   0          8h
words-84bccbc5d7-5qz7k   1/1     Running   0          8h
words-84bccbc5d7-fks2p   1/1     Running   0          8h
words-84bccbc5d7-lc8v9   1/1     Running   0          8h
words-84bccbc5d7-w8ln5   1/1     Running   0          8h
 
You must be able to successfully load/display the application in a web browser
SCHO http://localhost:8081
localhost image <img width="1284" alt="Screen Shot 2021-05-18 at 6 51 22 PM" src="https://user-images.githubusercontent.com/84278162/118733331-15365c80-b80a-11eb-8a8c-89907ae9039c.png">

You must be able to view/display pod CPU and RAM usage over time, on a graph.
% kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-rc3/aio/deploy/recommended.yaml

 % kubectl get pods --all-namespaces
NAMESPACE              NAME                                         READY   STATUS    RESTARTS   AGE
default                db-68df76f5d6-mnpk2                          1/1     Running   0          8h
default                web-6f496699bd-9rdxx                         1/1     Running   0          8h
default                words-84bccbc5d7-2h56x                       1/1     Running   0          8h
default                words-84bccbc5d7-5qz7k                       1/1     Running   0          8h
default                words-84bccbc5d7-fks2p                       1/1     Running   0          8h
default                words-84bccbc5d7-lc8v9                       1/1     Running   0          8h
default                words-84bccbc5d7-w8ln5                       1/1     Running   0          8h
kube-system            coredns-f9fd979d6-mb2k4                      1/1     Running   0          33h
kube-system            coredns-f9fd979d6-zfjdl                      1/1     Running   0          33h
kube-system            etcd-docker-desktop                          1/1     Running   0          33h
kube-system            kube-apiserver-docker-desktop                1/1     Running   0          33h
kube-system            kube-controller-manager-docker-desktop       1/1     Running   0          33h
kube-system            kube-proxy-h4zds                             1/1     Running   0          33h
kube-system            kube-scheduler-docker-desktop                1/1     Running   0          33h
kube-system            storage-provisioner                          1/1     Running   0          33h
kube-system            vpnkit-controller                            1/1     Running   0          33h
kubernetes-dashboard   dashboard-metrics-scraper-74db988864-74bz5   1/1     Running   0          33h
kubernetes-dashboard   kubernetes-dashboard-84f46948d6-whfx8        1/1     Running   0          33h

% kubectl proxy
Starting to serve on 127.0.0.1:8001
View your dashboard http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login
If you want to get the cpu and the mem graph, you need to install Kubernetes metrics server and follow the below steps
% kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard unchanged
serviceaccount/kubernetes-dashboard unchanged
service/kubernetes-dashboard unchanged
secret/kubernetes-dashboard-certs unchanged
secret/kubernetes-dashboard-csrf unchanged
secret/kubernetes-dashboard-key-holder unchanged
configmap/kubernetes-dashboard-settings unchanged
role.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
deployment.apps/kubernetes-dashboard configured
service/dashboard-metrics-scraper unchanged
deployment.apps/dashboard-metrics-scraper configured

% kubectl patch deployment kubernetes-dashboard -n kubernetes-dashboard --type 'json' -p '[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--enable-skip-login"}]'
deployment.apps/kubernetes-dashboard patched
% kubectl proxy
Starting to serve on 127.0.0.1:8001


Extra Credit

Host your Kubernetes cluster in AWS
#SCHO Used the repository SRE-AWS to build the Kubernetes cluster in my own AWS account,381775446117, using the Cloudformation template












<img width="1622" alt="Screen Shot 2021-05-18 at 10 56 04 PM" src="https://user-images.githubusercontent.com/84278162/118750328-30669380-b82d-11eb-8e57-c80b8d625d5e.png">



{
  "AWSTemplateFormatVersion": "2010-09-09",
  "Description": "Lab IDE using new public subnet for container workshop v0.5",
  "Metadata": {},
  "Parameters": {},
  "Mappings": {
    "SubnetConfig": {
      "VPC": {
        "CIDR": "10.0.0.0/16"
      },
      "Public": {
        "CIDR": "10.0.0.0/24"
      }
    }
  },
  "Conditions": {},
  "Resources": {
    "EksVpc": {
      "Type" : "AWS::CloudFormation::Stack",
      "Properties" : {
        "TemplateURL" : "https://amazon-eks.s3-us-west-2.amazonaws.com/1.10.3/2018-06-05/amazon-eks-vpc-sample.yaml"
      }
    },
    "EksServiceRole": {
      "Type" : "AWS::CloudFormation::Stack",
      "Properties" : {
        "TemplateURL" : "https://amazon-eks.s3-us-west-2.amazonaws.com/1.10.3/2018-06-05/amazon-eks-service-role.yaml"
      }
    },
    "VPC": {
      "Type": "AWS::EC2::VPC",
      "Properties": {
        "EnableDnsSupport": "true",
        "EnableDnsHostnames": "true",
        "CidrBlock": {
          "Fn::FindInMap": [
            "SubnetConfig",
            "VPC",
            "CIDR"
          ]
        },
        "Tags": [
          {
            "Key": "Application",
            "Value": {
              "Ref": "AWS::StackName"
            }
          },
          {
            "Key": "Network",
            "Value": "Public"
          }
        ]
      }
    },
    "PublicSubnet": {
      "Type": "AWS::EC2::Subnet",
      "Properties": {
        "VpcId": {
          "Ref": "VPC"
        },
        "CidrBlock": {
          "Fn::FindInMap": [
            "SubnetConfig",
            "Public",
            "CIDR"
          ]
        },
        "Tags": [
          {
            "Key": "Application",
            "Value": {
              "Ref": "AWS::StackName"
            }
          },
          {
            "Key": "Network",
            "Value": "Public"
          }
        ]
      }
    },
    "InternetGateway": {
      "Type": "AWS::EC2::InternetGateway",
      "Properties": {
        "Tags": [
          {
            "Key": "Application",
            "Value": {
              "Ref": "AWS::StackName"
            }
          },
          {
            "Key": "Network",
            "Value": "Public"
          }
        ]
      }
    },
    "GatewayToInternet": {
      "Type": "AWS::EC2::VPCGatewayAttachment",
      "Properties": {
        "VpcId": {
          "Ref": "VPC"
        },
        "InternetGatewayId": {
          "Ref": "InternetGateway"
        }
      }
    },
    "PublicRouteTable": {
      "Type": "AWS::EC2::RouteTable",
      "Properties": {
        "VpcId": {
          "Ref": "VPC"
        },
        "Tags": [
          {
            "Key": "Application",
            "Value": {
              "Ref": "AWS::StackName"
            }
          },
          {
            "Key": "Network",
            "Value": "Public"
          }
        ]
      }
    },
    "PublicRoute": {
      "Type": "AWS::EC2::Route",
      "DependsOn": "GatewayToInternet",
      "Properties": {
        "RouteTableId": {
          "Ref": "PublicRouteTable"
        },
        "DestinationCidrBlock": "0.0.0.0/0",
        "GatewayId": {
          "Ref": "InternetGateway"
        }
      }
    },
    "PublicSubnetRouteTableAssociation": {
      "Type": "AWS::EC2::SubnetRouteTableAssociation",
      "Properties": {
        "SubnetId": {
          "Ref": "PublicSubnet"
        },
        "RouteTableId": {
          "Ref": "PublicRouteTable"
        }
      }
    },
    "PublicNetworkAcl": {
      "Type": "AWS::EC2::NetworkAcl",
      "Properties": {
        "VpcId": {
          "Ref": "VPC"
        },
        "Tags": [
          {
            "Key": "Application",
            "Value": {
              "Ref": "AWS::StackName"
            }
          },
          {
            "Key": "Network",
            "Value": "Public"
          }
        ]
      }
    },
    "InboundSSHPublicNetworkAclEntry": {
      "Type": "AWS::EC2::NetworkAclEntry",
      "Properties": {
        "NetworkAclId": {
          "Ref": "PublicNetworkAcl"
        },
        "RuleNumber": "101",
        "Protocol": "6",
        "RuleAction": "allow",
        "Egress": "false",
        "CidrBlock": "0.0.0.0/0",
        "PortRange": {
          "From": "22",
          "To": "22"
        }
      }
    },
    "InboundEphemeralPublicNetworkAclEntry": {
      "Type": "AWS::EC2::NetworkAclEntry",
      "Properties": {
        "NetworkAclId": {
          "Ref": "PublicNetworkAcl"
        },
        "RuleNumber": "103",
        "Protocol": "6",
        "RuleAction": "allow",
        "Egress": "false",
        "CidrBlock": "0.0.0.0/0",
        "PortRange": {
          "From": "1024",
          "To": "65535"
        }
      }
    },
    "OutboundPublicNetworkAclEntry": {
      "Type": "AWS::EC2::NetworkAclEntry",
      "Properties": {
        "NetworkAclId": {
          "Ref": "PublicNetworkAcl"
        },
        "RuleNumber": "100",
        "Protocol": "6",
        "RuleAction": "allow",
        "Egress": "true",
        "CidrBlock": "0.0.0.0/0",
        "PortRange": {
          "From": "0",
          "To": "65535"
        }
      }
    },
    "PublicSubnetNetworkAclAssociation": {
      "Type": "AWS::EC2::SubnetNetworkAclAssociation",
      "Properties": {
        "SubnetId": {
          "Ref": "PublicSubnet"
        },
        "NetworkAclId": {
          "Ref": "PublicNetworkAcl"
        }
      }
    },
    "LabIDE": {
      "Description": "-",
      "Type": "AWS::Cloud9::EnvironmentEC2",
      "Properties": {
        "Description": "Lab IDE for container workshop",
        "AutomaticStopTimeMinutes": 60,
        "InstanceType": "t2.micro",
        "Name": {
          "Ref": "AWS::StackName"
        },
        "SubnetId": {
          "Ref": "PublicSubnet"
        }
      }
    },
    "KopsStateStore": {
      "Description": "-",
      "Type": "AWS::S3::Bucket",
      "Properties": {
        "VersioningConfiguration": {
          "Status": "Enabled"
        }
      }
    },
    "LabIdeRole": {
      "Type": "AWS::IAM::Role",
      "Properties": {
        "AssumeRolePolicyDocument": {
          "Version": "2012-10-17",
          "Statement": [
            {
              "Effect": "Allow",
              "Principal": {
                "Service": [
                  "ec2.amazonaws.com"
                ]
              },
              "Action": [
                "sts:AssumeRole"
              ]
            }
          ]
        },
        "Policies": [
          {
            "PolicyName": "eks-service",
            "PolicyDocument": {
              "Version": "2012-10-17",
              "Statement": [
                {
                  "Effect": "Allow",
                  "Action": [
                    "eks:*",
                    "cloudformation:CreateStack",
                    "cloudformation:UpdateStack"
                  ],
                  "Resource": "*"
                }
              ]
            }
          }
        ],
        "ManagedPolicyArns": [
          "arn:aws:iam::aws:policy/AmazonEC2FullAccess",
          "arn:aws:iam::aws:policy/AmazonRoute53FullAccess",
          "arn:aws:iam::aws:policy/AmazonS3FullAccess",
          "arn:aws:iam::aws:policy/IAMFullAccess",
          "arn:aws:iam::aws:policy/AmazonVPCFullAccess",
          "arn:aws:iam::aws:policy/AWSCloudFormationReadOnlyAccess"
        ],
        "Path": "/"
      }
    },
    "LabIdeInstanceProfile": {
      "Type": "AWS::IAM::InstanceProfile",
      "Properties": {
        "Path": "/",
        "Roles": [
          {
            "Ref": "LabIdeRole"
          }
        ]
      }
    },
    "AddRoleToInstance": {
      "Description": "Add LabIdeRole to Cloud9 IDE Instance",
      "Type": "Custom::AddRoleToInstance",
      "DependsOn": [
        "AddRoleToInstanceFunction",
        "KopsStateStore"
      ],
      "Properties": {
        "ServiceToken": {
          "Fn::GetAtt": [
            "AddRoleToInstanceFunction",
            "Arn"
          ]
        },
        "Region": {
          "Ref": "AWS::Region"
        },
        "StackName": {
          "Ref": "AWS::StackName"
        },
        "EnvironmentId": {
          "Ref": "LabIDE"
        },
        "LabIdeInstanceProfileName": {
          "Ref": "LabIdeInstanceProfile"
        },
        "LabIdeInstanceProfileArn": {
          "Fn::GetAtt": [
            "LabIdeInstanceProfile",
            "Arn"
          ]
        },
        "BucketName": {
          "Ref": "KopsStateStore"
        }
      }
    },
    "AddRoleToInstanceFunction": {
      "Type": "AWS::Lambda::Function",
      "Properties": {
        "Code": {
          "ZipFile": {
            "Fn::Join": [
              "\n",
              [
                "from __future__ import print_function",
                "import boto3",
                "import logging",
                "import json",
                "import time",
                "import traceback",
                "import cfnresponse",
                "",
                "logger = logging.getLogger()",
                "logger.setLevel(logging.INFO)",
                "",
                "def handler(event, context):",
                "    logger.debug('Event: {}'.format(event))",
                "    logger.debug('Context: {}'.format(context))",
                "    responseData = {}",
                "    ",
                "    # Immediately respond on Delete",
                "    if event['RequestType'] == 'Delete':",
                "        # Empty Bucket before CloudFormation deletes it",
                "        session = boto3.Session()",
                "        s3 = session.resource(service_name='s3')",
                "        try:",
                "            bucket = s3.Bucket(event['ResourceProperties']['BucketName'])",
                "            bucket.object_versions.delete()",
                "            ",
                "            logger.info('Bucket '+event['ResourceProperties']['BucketName']+' objects/versions deleted.')",
                "            cfnresponse.send(event, context, cfnresponse.SUCCESS, responseData, 'CustomResourcePhysicalID')",
                "        except Exception as e:",
                "            logger.error(e, exc_info=True)",
                "            responseData = {'Error': traceback.format_exc(e)}",
                "            cfnresponse.send(event, context, cfnresponse.FAILED, responseData, 'CustomResourcePhysicalID')",
                "    ",
                "    if event['RequestType'] == 'Create':",
                "        try:",
                "            # Open AWS clients",
                "            ec2 = boto3.client('ec2')",
                "            ",
                "            # Get the InstanceId of the Cloud9 IDE",
                "            instance = ec2.describe_instances(Filters=[{'Name': 'tag:Name','Values': ['aws-cloud9-'+event['ResourceProperties']['StackName']+'-'+event['ResourceProperties']['EnvironmentId']]}])['Reservations'][0]['Instances'][0]",
                "            ",
                "            # Create the IamInstanceProfile request object",
                "            iam_instance_profile = {",
                "                'Arn': event['ResourceProperties']['LabIdeInstanceProfileArn'],",
                "                'Name': event['ResourceProperties']['LabIdeInstanceProfileName']",
                "            }",
                "            ",
                "            # Wait for Instance to become ready before adding Role",
                "            instance_state = instance['State']['Name']",
                "            while instance_state != 'running':",
                "                time.sleep(5)",
                "                instance_state = ec2.describe_instances(InstanceIds=[instance['InstanceId']])",
                "            ec2.associate_iam_instance_profile(IamInstanceProfile=iam_instance_profile, InstanceId=instance['InstanceId'])",
                "            ",
                "            responseData = {'Success': 'Role added to instance'+instance['InstanceId']+'.'}",
                "            cfnresponse.send(event, context, cfnresponse.SUCCESS, responseData, 'CustomResourcePhysicalID')",
                "        except Exception as e:",
                "            logger.error(e, exc_info=True)",
                "            responseData = {'Error': traceback.format_exc(e)}",
                "            cfnresponse.send(event, context, cfnresponse.FAILED, responseData, 'CustomResourcePhysicalID')"
              ]
            ]
          }
        },
        "Handler": "index.handler",
        "Role": {
          "Fn::GetAtt": [
            "LambdaExecutionRole",
            "Arn"
          ]
        },
        "Runtime": "python2.7",
        "Timeout": "30"
      }
    },
    "LambdaExecutionRole": {
      "Type": "AWS::IAM::Role",
      "Properties": {
        "AssumeRolePolicyDocument": {
          "Version": "2012-10-17",
          "Statement": [
            {
              "Effect": "Allow",
              "Principal": {
                "Service": [
                  "lambda.amazonaws.com"
                ]
              },
              "Action": [
                "sts:AssumeRole"
              ]
            }
          ]
        },
        "Path": "/",
        "Policies": [
          {
            "PolicyName": "root",
            "PolicyDocument": {
              "Version": "2012-10-17",
              "Statement": [
                {
                  "Effect": "Allow",
                  "Action": [
                    "logs:CreateLogGroup",
                    "logs:CreateLogStream",
                    "logs:PutLogEvents"
                  ],
                  "Resource": "arn:aws:logs:*:*:*"
                },
                {
                  "Effect": "Allow",
                  "Action": [
                    "cloudformation:DescribeStacks",
                    "cloudformation:DescribeStackEvents",
                    "cloudformation:DescribeStackResource",
                    "cloudformation:DescribeStackResources",
                    "ec2:DescribeInstances",
                    "ec2:AssociateIamInstanceProfile",
                    "ec2:ReplaceIamInstanceProfileAssociation",
                    "iam:ListInstanceProfiles",
                    "iam:PassRole"
                  ],
                  "Resource": "*"
                },
                {
                  "Effect": "Allow",
                  "Action": [
                    "s3:*"
                  ],
                  "Resource": [
                    {
                      "Fn::GetAtt": [
                        "KopsStateStore",
                        "Arn"
                      ]
                    },
                    {
                      "Fn::Join": [
                        "",
                        [
                          {
                            "Fn::GetAtt": [
                              "KopsStateStore",
                              "Arn"
                            ]
                          },
                          "/*"
                        ]
                      ]
                    }
                  ]
                }
              ]
            }
          }
        ]
      }
    }
  },
  "Outputs": {
    "Cloud9IDE": {
      "Value": {
        "Fn::Join": [
          "",
          [
            "https://",
            {
              "Ref": "AWS::Region"
            },
            ".console.aws.amazon.com/cloud9/ide/",
            {
              "Ref": "LabIDE"
            },
            "?region=",
            {
              "Ref": "AWS::Region"
            }
          ]
        ]
      }
    },
    "EksServiceRoleArn": {
      "Value": {
        "Fn::GetAtt": ["EksServiceRole", "Outputs.RoleArn"]
      }
    },
    "EksVpcId": {
      "Value": {
        "Fn::GetAtt": ["EksVpc", "Outputs.VpcId"]
      }
    },
    "EksVpcSubnetIds": {
      "Value": {
        "Fn::GetAtt": ["EksVpc", "Outputs.SubnetIds"]
      }
    },
    "EksVpcSecurityGroups": {
      "Value": {
        "Fn::GetAtt": ["EksVpc", "Outputs.SecurityGroups"]
      }
    }
  }
}




Host your Docker image on dockerhub.com
#SCHO created the dockerhub account, "scho20210516" and linked it with the Github account, "scho20210516"
#SCHO The local image was created by:
cd k8s-wordsmith-demo
docker-compose build

% docker image ls | grep dockersample
dockersamples/k8s-wordsmith-web      latest                                                  c1858c040bb0   2 years ago     11.1MB
dockersamples/k8s-wordsmith-db       latest                                                  1ec1b68f9d19   3 years ago     38.2MB
dockersamples/k8s-wordsmith-api(demo)      latest                                            77b84213c1f6   3 years ago     87.5MB

All three images are in https://hub.docker.com/repository/docker/scho20210516/sre-teladoc

Deploy your application with Helm
Using the repo, SRE-AWS, instead of installing Kubernetes resources manually via kubectl, one can use Helm to install pre-defined Charts faster, with less chance of typos or other operator errors
# first, add the default repository, then update
helm repo add stable https://charts.helm.sh/stable
helm repo update
#to list all charts
helm search repo
#search for nginx
helm search repo nginx
#To add the Bitnami Chart repo to our local list of searchable charts:
helm repo add bitnami https://charts.bitnami.com/bitnami
#search all Bitnami Charts
helm search repo bitnami
helm search repo bitnami/nginx
#use Helm to deploy the bitnami/nginx chart
helm install mywebserver bitnami/nginx
#review the underlying Kubernetes services, pods and deployments, run:
kubectl get svc,po,deploy
#inspect this Deployment object in more detail 
kubectl describe deployment mywebserver
#To verify the Pod object was successfully deployed,
kubectl get pods -l app.kubernetes.io/name=nginx
#To get the complete URL of this Service, run
kubectl get service mywebserver-nginx -o wide





<img width="579" alt="Screen Shot 2021-05-18 at 9 49 18 PM" src="https://user-images.githubusercontent.com/84278162/118744770-ef698180-b822-11eb-9ffb-1916a787ab39.png">







Deploy your infrastructure with Terraform
#Usse https://github.com/scho20210516/sre-terraform to build AWS EKS 




Install a Kubernetes Dashboard
#SCHO - Use below instruction 

https://docs.aws.amazon.com/eks/latest/userguide/dashboard-tutorial.html








Install APM in your application (monitoring)


Double Extra Credit
Enable us to load your application in our browser


When you create a Kubernetes Ingress, an AWS Application Load Balancer is provisioned that load balances application traffic. To learn more, see What is an Application Load Balancer? in the Application Load Balancers User Guide and Ingress in the Kubernetes documentation. ALBs can be used with pods deployed to nodes or to AWS Fargate. You can deploy an ALB to public or private subnets.

Application traffic is balanced at L7 of the OSI model. To load balance network traffic at L4, you deploy a Kubernetes Service of type LoadBalancer, which provisions an AWS Network Load Balancer. For more information, see Network load balancing on Amazon EKS. To learn more about the differences between the two types of load balancing, see Elastic Load Balancing features on the AWS website.




Enable us to view pod CPU and RAM usage in New Relic
I created a free New Relic account and started integration with the Kubernetes but its taking more than usual. I have experience with the Datadog integration with Kubernetes, AWS, Cloudflare, Okta, Github, Snowflake and etc - so this is matter of getting the NewRelic support and research more on my side







<img width="1619" alt="Screen Shot 2021-05-18 at 11 10 50 PM" src="https://user-images.githubusercontent.com/84278162/118751139-ce0e9280-b82e-11eb-8461-721bc33b9433.png">







CD pipeline that builds and deploys on every commit/push

For kubenetes CD, I highly recommend using https://argoproj.github.io/argo-cd/

I have experience with AWS CodeDeploy but I never used it for the Kubernetes - I used it for the serverless mobile applications. 


