# `Purpose`

Test [URL Shortener Application deployemnt 3](https://github.com/kaedmond24/python_url_shortener_app_deployment_3) ability to handle unexpected traffic spikes when deployed on AWS Elastic Beanstalk.

Blitz 3 uses Apache JMeter to send multiple waves of traffic to the Python URL Shortener application to observe how well it can handle major traffic spikes. This observation can help determine what optimizations are needed to increase the applications resiliency.

### `Context`

During the US holidays, the URL Shortener is expected to receive unexpected traffic spikes between 900 - 1,000 requests per second. The URL Shortener can handle a maximum of 700 requests per second on a single T.2 micro. There are 12 government-recognized holidays in a year. What should you add to the infrastructure to handle the 900 - 1,000 unexpected requests per second?

## `System Diagram`

Blitz Infrastructure Architecture [Link](Blitz_3_Diagram.png)

## `Summary`

1. `Summary of the Issue.`
	
   - The URL Shortener application deployed using AWS Elastic Beanstalk on a T2.micro instance is expected to handle up to 700 web requests per second. However, on government holidays traffic is expected to spike to approximately 900-1000 web requests per second. In order to verify the applications ability to manage the expected traffic loads, a series of tests were performed against the web app. A wave of 700 requests per second followed by a wave of 1000 requests per second was sent to the application. The results of the tests revealed that the current application deployment would not be able to handle the holiday traffic. When web traffic is at 700 requests per second the application was able to service all of the requests successfully. When tests were run against the application using 1000 web requests per second the application failed to service all of the requests. An error was returned for 20 requests showing that the application could handle more that 700 requests per seconds but not enough to satisfy holiday expectations.

2. `What steps were taken to remediation the issue (if there were any).`

   - Remediating the applications issue with handling expected holiday traffic can be solved two ways. The first solution would be to vertically scale the container to a more powerful instance such as a c5.4xlarge. We went to the second option for remediation which involves horizontally scaling the application by adding an additional T2.micro instance. This was achieved by using AWS Elastic Beanstalk’s auto scaling functionality. The auto scaling feature allows multiple instances to be added to an auto scaling group created by Elastic Beanstalk. The group’s configuration allows it to use triggers or scheduling to know when scaling should occur. Also, a load balancer is created to manage traffic across multiple instances in the group. Once the application was redeployed with AWS Elastic Beanstalk auto scaling group configured, another test was run using the holiday traffic expectations (1000 requests per second). The results of the test were satisfactory. The application was now able to handle the expected holiday traffic surge by scheduling auto scaling to two instance during the holidays.

3. `The economic benefits of using auto-scaling and an ALB vs a stronger instance with more bandwidth (Like a c5.4xlarge).`

   - The cost benefits of using auto scaling with a load balancer outweighs the cost of deploying a more powerful instance due to the method in which they are used. With a more powerful instance you're looking at greater cost because that resource would most likely be running for longer periods of time with an elevated hourly charge. On the other hand, auto scaling allows you to dynamically deploy cheaper instances with more granular control limiting cost overruns. Furthermore, auto scaling provides the opportunity to scale without downtime. This can be extremely critical to some organizations that can lose a considerable amount of revenue during any type of downtime.
