# Introduction

This unit introduces cloud computing as the provision of computing resources. Students are exposed to modern systems architectures and software development kits that, together, provide cloud computing frameworks. Students will learn about different aspects of the design, development, provisioning and management of cloud-based applications.

Students will gain a sound understanding of cloud-based computing and the opportunities that it provides for a diverse range of computing applications. Special attention will be made to security of cloud based applications and the different strategies that are available in these deployments.

An overarching goal of the unit is to provide students with an opportunity to undertake problem identification, analysis and solution and to apply these skills to the field of Cloud Computing.

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

* Mid-semester Test: 30% of the unit grade. The test is worth 60 marks in total.
* Final Exam: 50% of the unit grade. The final is worth 100 marks in total. 
* Labs: 20% of the unit grade. The 9 labs are worth 20 marks in total. Labs 1–7 are worth 2 marks each. Labs 8–9 are worth 3 marks each.

### Live Lecture Materials

Recordings of live lectures are available (give up to 48 hours for it to be available), which are on LMS -> Lecture Recordings.

Live lecture slides will be made available early on Monday in each lecture week and can be downloaded from [our repo](https://github.com/zhangzhics/CITS5503_Sem2/tree/refs/heads/master/Live-lecture-slides) (live lecture slides in 2025 can also be found from the repo)

### Live Lab Assessment

Lab facilitators will assess each student's understanding of the labs in person during lab sessions. Each lab contains a few checkpoints listed at the bottom of its lab page. Every checkpoint has an assigned mark and is assessed on a binary basis: students will receive either its full value or zero, with no partial marks awarded.

Students have **one attempt only** to demonstrate each checkpoint successfully to a lab facilitator. If they fail a checkpoint during that attempt, no marks will be awarded for that checkpoint.

All checkpoints must be demonstrated live. Screenshots, prerecorded demonstrations, and other indirect evidence will not be accepted. Marks will be recorded and made available immediately after the assessment.

#### Queues During Lab Sessions

Two separate queues are available throughout each lab session:

* **Marking queue:** for students who have completed the required checkpoints and are prepared for a live assessment.
* **Q&A queue:** for students who need assistance or clarification about a lab, including its checkpoint requirements.

Both queues operate on a **first-come, first-served basis**. Lab facilitators will alternate between the two queues while preserving the order of students within each queue. If one queue is empty, facilitators will continue serving the other.

The two queues serve different purposes and must remain separate:

* The **marking queue is for live assessments only**: students will not ask questions or request assistance or clarification.
* The **Q&A queue is for questions and assistance only**. Live demonstrations will not be assessed in this queue.
* After receiving assistance through the Q&A queue, students who wish to complete an assessment must join the end of the marking queue.

#### Marking Queue Rules

* Before joining the marking queue, students must complete all required checkpoints and be ready to demonstrate them without assistance.
* Each turn covers **one student and the checkpoints for one lab only** and should take a few minutes.
* To demonstrate checkpoints for another lab, students must rejoin the end of the marking queue.
* If a student is not assessed before the lab session ends, they may attend another scheduled lab session and join its marking queue.

#### Lab Due Dates

* **Labs 1–5:** Due by the end of the last scheduled lab session in **Week 7 (7–11 September)**.
* **Labs 6–9:** Due by the end of the last scheduled lab session in **Week 11 (5–9 October)**.

**Note:** The lab session timetable is provided below. During Week 7, an additional **Labs 1–5 Marking Queue** will operate exclusively for assessing Labs 1–5. Facilitators may prioritise this queue depending on demand. 

Students must complete their live assessments progressively and must not wait until the final week before the relevant deadline to demonstrate multiple labs. Upon the end of each lab session, facilitators will complete only the assessment already in progress and will not begin assessing additional students, even if the marking queue has not been cleared. So staying in the marking queue does not guarantee that a student will be assessed before the session ends.

Students with special consideration approved by the student office may receive **one extension for each lab deadline listed above, for a maximum of 7 calendar days**.

#### Lab Session Timetable

Students may attend any lab session below that suits their availability. 

<table data-search="false">
<thead>
<tr>
<th>Day</th>
<th>Time</th>
<th>Location</th>
</tr>
</thead>
<tbody>
<tr>
<td>Tuesday</td>
<td>2:00 pm–4:00 pm</td>
<td>CSSE Computer Lab 241.203</td>
</tr>
<tr>
<td>Tuesday</td>
<td>4:00 pm–6:00 pm</td>
<td>CSSE Computer Lab 241.203</td>
</tr>
<tr>
<td>Wednesday</td>
<td>10:00 am–12:00 pm</td>
<td>CSSE Computer Lab 241.205</td>
</tr>
<tr>
<td>Wednesday</td>
<td>12:00 pm–2:00 pm</td>
<td>CSSE Computer Lab 241.205</td>
</tr>
<tr>
<td>Wednesday</td>
<td>4:00 pm–6:00 pm</td>
<td>CSSE Computer Lab 241.205</td>
</tr>
<tr>
<td>Thursday</td>
<td>10:00 am–12:00 pm</td>
<td>CSSE Computer Lab 241.205</td>
</tr>
<tr>
<td>Thursday</td>
<td>2:00 pm–4:00 pm</td>
<td>CSSE Computer Lab 241.203</td>
</tr>
<tr>
<td>Thursday</td>
<td>4:00 pm–6:00 pm</td>
<td>CSSE Computer Lab 241.205</td>
</tr>
<tr>
<td>Friday</td>
<td>2:00 pm–4:00 pm</td>
<td>CSSE Computer Lab 241.205</td>
</tr>
</tbody>
</table>

## Use of AWS

The unit is based on the cloud services of Amazon Web Service (AWS) and a range of open source software. It is not possible in a course such as this to cover all of the products that these cloud services provide. The theme has been to concentrate on the most common use cases of \[a] using AWS to deploy web applications utilising data sources such as databases and \[b] data analytics and machine learning.

### AWS Resource Usage Rules for Labs

Students will be provided with AWS login credentials upon the end of **Week 1**. To ensure fair access to shared AWS resources and avoid unnecessary costs, all students must follow the resource management rules below when walking through their labs:

* When starting Lab 1, students must use their student number to identify their assigned AWS region in the provided region table (also in Lab 1). All AWS resources must be created in the assigned region.
* After a lab is completed, its AWS resources must be **deleted/terminated manually from the AWS management console by students**.
* Every EC2 instance should be stopped **by 10pm every night by students** and deleted/terminated at **the same time every Friday night by us**. Students can start their instance(s) **after 7am every morning**. If a student is detected creating/starting EC2 instances **between 10pm and 7am on three occasions**, their access to AWS will be revoked, and then they will need to create their own AWS accounts using their own credit cards. We will send reminders for the first two occasions.
* The maximum number of EC2 instances required for a lab is 2. Therefore, do not have more than 2 instances simultaneously. Otherwise, instances of the same student will be deleted or terminated from the oldest to the newest **at 10pm each night by us** until only two instances remain.
* Lab 5 requires application load balancers (ALBs). All ALBs and their associated resources, such as listeners, will be deleted **at 10pm every night by us**. Students may recreate their ALBs **after 7am every morning**. If a student is detected creating an ALB **between 10pm and 7am on three occasions**, their access to AWS will be revoked, and then they will need to create their own AWS accounts using their own credit cards. We will send reminders for the first two occasions.

## Use of AI Tools

Students may make limited use of AI tools (Tier 2: AI assistance, outlined in the [UWA guide](https://www.uwa.edu.au/students/-/media/project/uwa/uwa/students/academic-support/using-artificial-intelligence-tools-at-uwa---a-guide-for-students-%282026%29.pdf)) to support learning and troubleshooting. For example, to clarify concepts, look up relevant AWS CLI commands, interpret error messages, or understand how python API works. However, students must not use AI during the live lab assessment.

When using any public AI system (e.g., ChatGPT, and Google Gemini), students must not input personal data, and must not upload or paste any copyrighted material that is not their own, including UWA or staff materials such as lecture slides, handouts. Also, DeepSeek must not be used in any form due to safety and security concerns raised by the Department of Home Affairs (refer to the [UWA guide](https://www.uwa.edu.au/students/-/media/project/uwa/uwa/students/academic-support/using-artificial-intelligence-tools-at-uwa---a-guide-for-students-%282026%29.pdf) for more details).

Both mid-sem test and final exam are invigilated. So no AI tools are allowed during the test/exam.


## License Terms

Except where otherwise specified, the text in this course is licensed under the Creative Commons Attribution-ShareAlike License 4.0 (International) (CC-BY-SA 4.0).

Contents on this page are further developed by Zhi Zhang (2023\~) and Jichunyang Li (2024\~).

