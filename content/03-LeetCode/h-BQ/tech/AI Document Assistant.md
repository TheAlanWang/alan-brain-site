- Built a cloud-native **full-stack** platform enabling real-time document summarization, translation, and Q&A.
- Designed scalable architecture with **PostgreSQL**, and **JWT authentication**, ensuring secure and reliable request handling.
- Containerized with **Docker**; deployed on **AWS EC2**; reduced latency 40% via caching + streaming.

### Q: What is the biggest challenge you have faced?
**S (Situation)**  
In my previous role, our company had an HR system, running on a single local machine, only HR manage it. But, as the company grew, too much manual works. It became a bottleneck and didn’t integrate with other internal systems like company workflow tools.
**T (Task)**  
My task is to build a new HR system within strict time. I have to choose vendor, plan the migration, coordinating with other teams. The most important part is keep the data safe and secure.
**A (Action)**  
I approached it in three steps:
1. **Clarified scope and integration needs** — I mapped out all the interfaces/APIs we would need, confirmed what HR data fields were required, and aligned the workflow with teams like IT and operations.    
2. **Reduced risk with secure testing** — since HR data is sensitive, I created a dummy dataset and schema so we could test vendor integrations and API flows without exposing real employee information.
3. **Phased delivery to meet the deadline** — I proposed splitting the rollout into two phases:
    - Phase 1: core HR functions (employee records, approvals, basic workflows)
    - Phase 2: payroll/salary-related features and calculations
**R (Result)**  
We successfully delivered **Phase 1 on schedule** and rolled it out before the end of the year, and all employees were able to use it to participate in the performance review process. The phased approach also reduced risk and made it easier for users to adopt the system gradually. From that experience, I learned that for data migrations, data security, phased delivery, tight cross-team communication are the key to success.

**I’d like to improve Adobe Acrobat’s PDF-to-Word conversion feature.**  
A lot of people rely on it for resumes, contracts, and reports, but the output can become unreliable when the PDF has complex layout—like multi-column text, tables, or mixed text and images. When the structure isn’t preserved, users spend a lot of time cleaning up the Word file, which defeats the purpose of “export.”

**If I were improving it, I’d focus on three things:**
1. **Structure preservation**: detect reading order and document hierarchy better—headings, paragraphs, lists, and tables should remain consistent.
2. **Better table handling**: improve cell boundary detection and merged-cell logic, and keep table styles stable in Word.
3. **User control and transparency**: offer export modes like _“Prioritize layout”_ vs _“Prioritize text flow”_, and show a quick preview with highlighted “low-confidence” regions so users can fix issues faster.

**To measure success**, I’d track conversion quality metrics (like structure match rate), reduction in post-edit time, and user satisfaction/return rate for the export feature.



---
Q: Why did you decide to start this project?
A: While working at a finance firm, I had to handle a large volume of documents—such as contracts and technical documentation—which often took up too much time. I wanted to build a system that could help streamline the process and save time for myself and others.

你如何处理并发请求的性能问题？还有，你是如何实现文档内容的安全性和隐私保护的？

Q: What is the reason for choosing the specific tech stack?
I chose React for its excellent front-end interactivity, and FastAPI on the back-end for its extensibility in Python, allowing future integration of RAG. I used PostgreSQL as it’s free and powerful, deployed on AWS EC2, with CI/CD for automated deployment.

Q: How does your architecture support scalability?
My architecture supports scalability by leveraging cloud infrastructure (AWS EC2), using containerization with Docker for easy deployment, employing a RESTful API with FastAPI, and relying on PostgreSQL, which handles large datasets and can be optimized for scaling horizontally or vertically.

Q: How specifically did you achieve scalability?  
A: I achieved scalability by leveraging elastic cloud resources, such as AWS EC2, and deploying with Docker containers for horizontal scaling. Additionally, I optimized PostgreSQL with sharding and indexing to ensure efficient queries even as data volume increases.

Question: How do you ensure scalability, especially when multiple users are uploading documents simultaneously, and how do you maintain low latency for them?
Answer: I ensure scalability by leveraging AWS EC2 for elastic resources and using Docker containers for horizontal scaling. I implement asynchronous processing with FastAPI endpoints. When multiple users upload PDFs, tasks are placed in a Celery queue backed by Redis. The worker nodes process these tasks efficiently in the background, and results are returned via callbacks. This way, even under high load, the system maintains low latency and a smooth user experience.

Question: How do you handle a high volume of concurrent requests, especially when processing large file uploads?
Answer: I handle a high volume of concurrent requests by using asynchronous FastAPI endpoints. When a large file is uploaded, the endpoint awaits the file processing, allowing the server to continue handling other requests. Additionally, I offload longer processing tasks to a Celery queue, ensuring the system remains responsive and scalable.

Question: What is the difference between synchronous and asynchronous handling in file uploads?  
Answer: In synchronous handling, each file upload must complete before the next begins, blocking other requests. In asynchronous handling, multiple uploads can be accepted simultaneously, and each file is processed independently, allowing the system to handle many uploads without waiting.


