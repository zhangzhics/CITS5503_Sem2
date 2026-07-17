# Introduction

This unit introduces cloud computing as the provision of computing resources. Students are exposed to modern systems architectures and software development kits that, together, provide cloud computing frameworks. Students will learn about different aspects of the design, development, provisioning and management of cloud-based applications.

Students will gain a sound understanding of cloud-based computing and the opportunities that it provides for a diverse range of computing applications. Special attention will be made to security of cloud based applications and the different strategies that are available in these deployments.

An overarching goal of the unit is to provide students with an opportunity to undertake problem identification, analysis and solution and to apply these skills to the field of Cloud Computing.

## Note on the use of AWS

The course is based on the cloud services of Amazon Web Services and a range of open source and other products. It is not possible in a course such as this to cover all of the products that these cloud services provide. The theme has been to concentrate on the most common use cases of \[a] using AWS to deploy web applications utilising data sources such as databases and \[b] data analytics and machine learning.

### AWS Resource Usage Rules

To ensure fair access to the shared AWS resources and to avoid unnecessary costs, all students must follow the resource management rules below:

* When starting Lab 1, students must use their student number to identify their assigned AWS region in the provided region table (also in Lab 1). All AWS resources must be created in the assigned region.

* After a lab is completed, its AWS resources must be **deleted/terminated manually from the AWS management console by students**.

* Every EC2 instance should be stopped **by 10pm every night by students** and deleted/terminated at **the same time every Friday night by us**. Students can start their instance(s) **after 7am every morning**. If a student is detected creating/starting EC2 instances **between 10pm and 7am on three occasions**, their access to AWS will be revoked, and then they will need to create their own AWS accounts using their own credit cards. We will send reminders for the first two occasions.

* The maximum number of EC2 instances required for a lab is 2. Therefore, do not have more than 2 instances simultaneously. Otherwise, instances of the same student will be deleted or terminated from the oldest to the newest **at 10pm each night by us** until only two instances remain.

* Lab 5 requires application load balancers (ALBs). All ALBs and their associated resources, such as listeners, will be deleted **at 10pm every night by us**. Students may recreate their ALBs **after 7am every morning**. If a student is detected creating an ALB **between 10pm and 7am on three occasions**, their access to AWS will be revoked, and then they will need to create their own AWS accounts using their own credit cards. We will send reminders for the first two occasions.

## Learning Outcomes

Students are able to

1. Understand cloud services, there motivation, design and implementation
2. Understand the basics of virtualisation of hardware, networks and security
3. Understand application architectures and how they meet specific requirements and needs
4. Understand how to achieve scalability and security in a cloud-based architecture
5. Use DevOps to deploy and manage the creation and updating of software environments
6. Use cloud services to carry out specific use cases such as data analytics, machine learning and other artificial intelligence tasks
7. Write code in Python using a variety of SDKs to achieve the above where appropriate

## Contents and Assessments

The course consists of 12 weeks of lectures and 9 weeks of practical labs. Assessments are listed below.

* 20% Labs
* 30% Mid-sem Test
* 50% Final Exam

## Live Lectures

Recordings of live lectures are available (give up to 48 hours for it to be available), which are on LMS -> Lecture Recordings.



Live lecture slides will be made available early on Monday in each lecture week and can be downloaded from [our repo](https://github.com/zhangzhics/CITS5503_Sem2/tree/refs/heads/master/Live-lecture-slides) (live lecture slides in 2025 can also be found from the repo)



## AI Tools

For lab assignments, students may make limited use of AI tools (Tier 2: AI assistance, outlined in the [UWA guide](https://www.uwa.edu.au/students/-/media/project/uwa/uwa/students/academic-support/using-artificial-intelligence-tools-at-uwa---a-guide-for-students-%282026%29.pdf)) to support learning and troubleshooting. For example, to clarify concepts, look up relevant AWS CLI commands, interpret error messages, or understand how python API works. However, submissions must reflect a student's own work. Students must not use AI to generate complete solutions (e.g., step-by-step descriptions that students simply follow without understanding). Students are responsible for verifying the accuracy of any AI output, and should keep a brief record of prompts and outputs in case questions arise.

When using any public AI system (e.g., ChatGPT, and Google Gemini), students must not input personal data, and must not upload or paste any copyrighted material that is not their own, including UWA or staff materials such as lecture slides, handouts. Also, DeepSeek must not be used in any form due to safety and security concerns raised by the Department of Home Affairs (refer to the [UWA guide](https://www.uwa.edu.au/students/-/media/project/uwa/uwa/students/academic-support/using-artificial-intelligence-tools-at-uwa---a-guide-for-students-%282026%29.pdf) for more details).

Both mid-sem test and final exam are invigilated. So no AI tools are allowed during the test/exam.

## License Terms

Except where otherwise specified, the text in this course is licensed under the Creative Commons Attribution-ShareAlike License 4.0 (International) (CC-BY-SA 4.0).

Contents on this page are further developed by Zhi Zhang (2023\~) and Jichunyang Li (2024\~).

