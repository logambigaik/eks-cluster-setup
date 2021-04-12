# EKS clustercreation using eksctl:

# Step1: Take EC2 Instance with t2.xlarge instance type
# Step2: Create IAM Role with Admin policy for eks-cluster and attach to ec2-instance .Also do aws configure

![image](https://user-images.githubusercontent.com/54719289/113122763-d7568800-920b-11eb-907d-ba4b39f84216.png)

![image](https://user-images.githubusercontent.com/54719289/113121786-e7219c80-920a-11eb-938a-b08a9670c266.png)

# Step3: Install kubectl
	curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/kubectl
	chmod +x ./kubectl
	mkdir -p $HOME/bin
	cp ./kubectl $HOME/bin/kubectl
	export PATH=$HOME/bin:$PATH
	echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
	source $HOME/.bashrc
	kubectl version --short --client

# Step4: Install eksctl:
    curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
    sudo mv /tmp/eksctl /usr/bin
    eksctl version

# Step5: Cluster creation:
    eksctl create cluster --name=eksdemo \
                      --region=us-east-1 \
                      --zones=us-east-1a,us-east-1b \
                      --without-nodegroup 

![image](https://user-images.githubusercontent.com/54719289/113126467-93fe1880-920f-11eb-8643-23b5bec09ab3.png)

In case , you encounter any error, run the below command,

	For example eks-demo for london ,
		eksctl create cluster --name=eksdemo \
                  --region=eu-west-2 \
                  --zones=eu-west-2a,eu-west-2b \
                  --without-nodegroup 

	to check the subnet available in region: aws ec2 describe-subnets --region=eu-west-2
	
	eksctl utils describe-stacks --region=eu-west-2 --cluster=eksdemo
	
	![image](https://user-images.githubusercontent.com/54719289/114469429-0e9e3f00-9be5-11eb-8331-84216eb90b6e.png)

	
	
![image](https://user-images.githubusercontent.com/54719289/114321721-193cd380-9b14-11eb-9925-f7fa3f0ecd67.png)

	if its related to authorization error, do aws configure that will fix the error, 
	Also not sure about the subnets then use --fargate--> it picks the default vpc, subnets
	

		      
					  
# Step6: Add Iam-Oidc-Providers:

Note: connect aws services from Kubernetes we require oidc provider,create sa and then it will attach to oidc Provider

    eksctl utils associate-iam-oidc-provider \
        --region us-east-1 \
        --cluster eksdemo \
	--approve
		
![image](https://user-images.githubusercontent.com/54719289/113127899-2f43bd80-9211-11eb-901f-c4d0bb6144db.png)

# Step7: Create node-group:
    eksctl create nodegroup --cluster=eksdemo \
                       --region=us-east-1 \
                       --name=eksdemo-ng-public \
                       --node-type=t2.large \
                       --nodes=2 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=10 \
                       --ssh-access \
                       --ssh-public-key=archu-acc \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access
		       
![image](https://user-images.githubusercontent.com/54719289/113127703-f4da2080-9210-11eb-9a74-c04f07e98159.png)

![image](https://user-images.githubusercontent.com/54719289/113128196-7631b300-9211-11eb-954a-f360dd2dfb5a.png)



					   
# CleanUP
Delete node-group:
			   
    eksctl delete nodegroup --cluster=eksdemo \
                       --region=us-east-1 \
		       	--name=eksdemo-ng-public
Delete Cluster:
				   
    eksctl delete cluster --name=eksdemo \
                      --region=us-east-1					   			   
