## Weekly Questions CITS5503 2022

## Week 1:

[1.0] [5 points] The evolution of Cloud Computing has been compared to the evolution of electricity supply as a utility. Describe specific problems that Cloud Computing solves as compared to businesses running their own data centres.

[2.0] [5 points] Describe the different categories of services (XaaS) cloud computing can provide with specific examples of each service.

[3.0] [10 points] An established financial company is about to launch their new banking application. Give 5 reasons why the company should use their own data centre rather than cloud computing.

[4.0] [20 points] Describe the concepts of vertical and horizontal scale. Describe 2 different ways in which you could scale a web application horizontally. Describe a potential architecture to scale the database to handle the scaling out of the web servers.

[5.0] [20 points] How could a mobile device benefit from cloud computing? Explain the reasons or provide your arguments supporting the contrary. Discuss several cloud applications for mobile devices; explain which one of the three cloud computing delivery models, SaaS, PaaS, or IaaS, would be used by each one of the applications and why.

## Week 2: 

[5.0] [20 points] Describe the steps which you would take on AWS and the decisions that would need to be made to create, configure and run a Virtual Machine Instance.

[6.0] [10 points] Describe EBS and what features it offers. 

[7.0] [10 points] What is CLI and Boto? What are advantages of using CLI? How does Boto function helps in AWS operation? 

## Week 3: 
[8.0] [10 points] Describe what virtualisation is and describe the characteristic attributes of the different types of virtualisation (Language, Operating System and Hardware).

[9.0] [10 points] Describe what containers are with reference to Docker and discuss their similarities and differences from operating system virtualisation perspective as provided by VirtualBox.
[10.0] [10 points] There was an evolution of operating system during the half century from 1960 to 2010. Identify the virtualisation milestones in this above evolution and explain them briefly. 

## Week 4: 
[10.0] [5 points] You are asked to store data about music albums in a DynamoDB table. For each album, you need to record the title of the album and the artist name. Describe the commands you would use to create a table to store such information and write an entry to that table in DynamoDB.

[11.0] [5 points] Describe how S3 handles consistency of objects and how this approach affects the state of objects when they are read using a GET.

[12.0] [5 points] What are the core components of DynamoDB

[13.0] [5 points] When a Bucket is created, AWS allows the specification of a number of features that can be managed. What are the key properties and features?

[14.0] [5 points] We can leave S3 buckets open to public. Is this suitable for a specific application? Why and why not? Justify your answer. 

## Week 5:

[14] [10 points] An organisation has 5 departments and has separated out each of the IAM users into separate groups using paths following the pattern companybucket/department1/*, companybucket /department2/*, companybucket /department3/* etc.

Their IAM account names follow the pattern user@department1.company.com, user@department2.company.com etc.

You are tasked with securing a bucket that contains a folder for each of 5 departments in an organisation. Only people within a department can write to their own folder. Everyone can read from all folders.  

Discuss the principles that you would use to create a policy that would achieve this objective.

Write the policy as a JSON file that you would use.

Note: you can have individual statements for each department.

[15] [5 points]  What aspects of security does the OSI Security Architecture X.800 standard cover? Which particular components of this standard does AWS Identity and Access Management deal with?

[16] [5 points] Name 3 of the keys that you would find in a Policy. Explain their role. An example of a key is “Version” that specifies the version of the policy syntax and is normally “Version”: “2012-10-17”


