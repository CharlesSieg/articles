# First Best Practices in AWS

## Orchestrate the entire infrastructure, including the VPC.

It's common practice to create CloudFormation scripts for deploying AWS resources like EC2 instances or ElastiCache clusters but a lot of shops don't go to the level of scripting the creation of the entire VPC. I think a lot of this reluctance comes from the fact that many new DevOps engineers are former developers who have a solid understanding of web servers and databases but consider the pure networking aspects of VPC to be a black box.

You certainly don't have to do this: for modern AWS accounts, AWS automatically creates a very nice default VPC. You can launch an EC2 instance trivially without any further VPC modification. But using the default VPC, in my opinion, is only for experimental playground-type environments, not for serious production deployments with an expectation of scalability, high-availability, and security.

There is a great deal of freedom and flexibility to be gained by taking the extra steps to learn how to script out the deployment of a completely new VPC, known as a "nondefault" VPC. Yes, there is a little bit of networking to be learned: CIDR blocks, subnets, routing tables, Internet gateways, network ACLs, and security groups. It can be overwhelming but there is a lot of help out there in the form of YouTube videos, AWS documentation, GitHub repos with example scripts, and video curriculum at Cloud Academy, A Cloud Guru, and Linux Academy, for starters.

Once you achieve this level of control in your orchestration scripts, you will be able to launch an *ENTIRE* environment with a single CLI command and tear it down later, leaving behind not a single trace (other than CloudWatch logs and an AWS bill) that it ever existed. A finer example of "Leave No Trace" there never was.



## Separate your environments.

Most development shops have an array of environments: sandbox, development, testing, staging, and production to name a few. A best practice is to have separate environments for each of these

There are a few ways to do this but, regardless of the solution you choose, it will be safer, more secure, and more productive for you to keep your AWS resources for various environments isolated from each other.



### But first, the ways not to do this:

**Tagging.** You could simply tag some resources as “development” and others as “production”. This has the advantage of working within a single AWS account and within a single VPC. However, this gets unwieldy quickly and is difficult to break apart later. It’s also a nightmare managing security groups, subnets, and routing tables and keeping things separate and secure.

**Separate VPCs.** You could also have a “development VPC” and a “production VPC”. This keeps resources isolated in their own virtual networks, which is better than having everything in a single VPC. However, when using the AWS Management Console, all of your resources are still, by default, all jumbled together. For example, when looking at the list of EC2 instances, they are all listed together and the only way to tell what is what is to look at the VPC column to see which environment they are associated with.

Both of these solutions also have the challenge of managing IAM in that you will need 
Best practice in this is to create separate AWS accounts for your different environments.


Enable consolidated account billing. Costs are automatically broken out by environment.

Favor immutability over configuration creep.
