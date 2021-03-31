# EKS clustercreation using eksctl:

# Step1: Take EC2 Instance with t2.xlarge instance type
# Step2: Create IAM Role with Admin policy for eks-cluster and attach to ec2-instance

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
		      
					  
# Step6: Add Iam-Oidc-Providers:
    eksctl utils associate-iam-oidc-provider \
        --region us-east-1 \
        --cluster eksdemo \
	--approve
					  
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
                       --ssh-public-key=ekscluster \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access	
					   
# CleanUP
Delete node-group:
			   
    eksctl delete nodegroup --cluster=eksdemo \
                       --region=us-east-1 \
		       	--name=eksdemo-ng-public
Delete Cluster:
				   
    eksctl delete cluster --name=eksdemo \
                      --region=us-east-1					   			   
