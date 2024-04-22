# *Background Processing using Hangfire*

What is a BACKGROUND PROCESS?
" A process that runs behins the scenes without user intervention."

>Question: Why even need background processing?
>Answer: Lets say at the end of the day you need to run some database updates, doing that manually is a lot of work & time consuming, so this situation by using background proceesing.

Examples of background processing:
1) Invoice generation
2) Monthly reports
3) Automatic subscription renewal
4) Email upon sign up. (Sending welcome email)

Background job/task types:-
1) FIRE & FORGET
 eg: When a user signs up, you send a user a 'Welcome Email' and you only send it once.
 
2) DELAYED
  Delayed tasks are like fire and forget, but you do not excute them as soon as the action is taken instead you define a time when this background job should work.
  eg: When you want to send an user a discount/voucher hours after they've signed up.
  
3) PERIODIC & SCHEDULED
  This is the type where the action/ the background task is processed periodically based on a schedule.
  eg1: When you send users marketting emails.
  eg2: Generating invoice to the users by the end of each month.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
>HANGFIRE : Is a free tool to perform background processing in .Net.

WHY USE HANGFIRE?
1) Reliable & Persistent : once the job is created Hangfire takes the responsibility.
  Eg: When you send email to users & you loose internet connection, Hangfire will try again once the connection/app is back up online and its persistent since data is stored in persistent storage which in our case is sql server.

2) Transparent & Distributed : Hangfire dashboard and more processing power is added.
 The Hangfire dashboard will you give you the real time data as it is being processed.

  
  
  -------------------
 Steps for setting up development environment :
 
1) Make sure hangfire is installed for your application
 NuGet Package Manager> browse> Hangfire

 
2) Solution Explorer > Create new SQL Server database > Server name: Copy-paste from your SQL Server Management Studio >New Database name: (name of your choice).

3) Update the connection string value in Startup.cs with: Right click on newly added connection in solution explorer > properties > Connection string value.

4) Create a Controllers folder> Add a HangfireController with a basic Get() method in it 
 >
  
    [ApiController]
    [Route("api/[controller]")]
    public class HangfireController : ControllerBase
    {
        [HttpGet]
        public IActionResult Get()
        {
            return Ok($"Hello from hangfire web api project demo");
        }
       
    }

 5) Properties > launchSettings.json > add this field>  "launchUrl": "hangfire" in 2 places
    
     a) Under "profiles" and your project name
     b) Under "IIS Express"

    And in the same file change the port for the application URL
     >"applicationUrl": "https://localhost:5001;http://localhost:5000",
     
     Save all changes and run the application
    ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/a8cbc01b-d6b4-4d22-9586-c6bf7dd4bdd8)

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

1. *FIRE & FORGET JOBS*

   When you use enqueue() method to create a fire & forget job, you can also get the job id

   > BackgroundJob.Enqueue(() => SendWelcomeEmail("Welcome to our app"));
   this line can be modified as below
   
   > var jobId = BackgroundJob.Enqueue(() => SendWelcomeEmail("Welcome to our app"));

   Code snippet:
   >
    
        [HttpPost]
        [Route("[action]")]
        public IActionResult Welcome()
        {
            var jobId = BackgroundJob.Enqueue(() => SendWelcomeEmail("Welcome to our app"));

            return Ok($"Job ID: {jobId}. Welcome email sent to the user!");
        }
        public void SendWelcomeEmail(string text)
        {
            Console.WriteLine(text);
        }


   Now we use POSTMAN to send a POST Request

   MANDATORY STEP : Your VS application should be running and you should be able to see hangfire dashboard
   
   STEP 2: Open POSTMAN > Copy the url from Hangfire dashboard i.e, https://localhost:5001/hangfire
   
   STEP 3: Make a POST request call, paste the url followed by Action method that you want to call
   https://localhost:5001/hangfire/Welcome

   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/46f7ff08-be74-4cf6-a1dd-7a06e5f1424c)

   This will give 404 not found, because you have missed to append '/api' since the route of the controller is   [Route("api/[controller]")]  [Route("[action]")]

   Corrected Post Url as below

   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/c10a020e-52cd-42db-a781-46e01f0489b2)

   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/507cb0d1-ba80-4967-9ef1-873a44df08e7)

   click on jobId to see more details

   You can also have the dashboard and PostMan side-by-side to see the real time processing

   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/0a01cac0-057f-464e-80aa-a2450207d92b)


   ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

2.  *DELAYED JOBS*

      "Its a fire & forget job but we get to define when the job is processed."

   Instead of enqueue(), here we use Schedule() method which will take the timestamp as the second parameter.

   >var jobId = BackgroundJob.Schedule(() => SendWelcomeEmail("Welcome to our app"), TimeSpan.FromSeconds(30));
   >which says, process this job 30 secs after i have created.

   Code snippet:
   >
    
       [HttpPost]
        [Route("[action]")]
        public IActionResult Discount()
        {
            int timeInSeconds = 30;
            var jobId = BackgroundJob.Schedule(() => SendWelcomeEmail("Welcome to our app"), TimeSpan.FromSeconds(timeInSeconds));

            return Ok($"Job ID: {jobId}. Discount email will be sent in {timeInSeconds} seconds!");
        }


   Now we use POSTMAN to send a POST Request

   Make a POST request call, paste the url followed by Action method that you want to call
   https://localhost:5001/api/hangfire/discount

   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/4ccf7c40-6779-46c4-af3e-c7855f5f9f88)


   Open the Hangfire dashbaord, now you can see you discount job in the Scheduled Tab Under jobs
     
   This discount job will be moved to Succeeded Tab Under jobs after 30 secs as per its implementation.
     
   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/a430388c-a027-46d8-b406-17bbb7cf0286)

   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/14ee0f01-57d2-45ab-b947-bf47b2688b6c)


   ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 3.  *RECURRING JOBS*

     "These are the jobs that need to be processed in a recurring way based on the timestamp that we get to define"

    eg: You have a web app where users signed up for your news letters and you send them news letter every first day of the month"

    Use case includes, checking if you have new data in your database every 5 minutes or when you want to send a report to all users each end of the month.

   So any task that happens recursively on a certain timestamp can be processed using a recurring job.

   Here i'm gonna create a job called 'Report'

   I will name the my api endpoint as DatabaseUpdate because i want to check if i have new data in my database.

   >  RecurringJob.AddOrUpdate(() => Console.WriteLine("Database updated"), Cron.Minutely);
   I want to check this change every minute, hence passing it as second parameter.

   Code snippet:
   >
    
        [HttpPost]
        [Route("[action]")]
        public IActionResult DatabaseUpdate()
        {
            RecurringJob.AddOrUpdate(() => Console.WriteLine("Database updated"), Cron.Minutely);
            return Ok("Database check job initiated!");
        }



   Before execution
     ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/a7471819-bf45-4ce3-90a6-48f4b9688e1d)


   Now we use POSTMAN to send a POST Request

   Make a POST request call, paste the url followed by Action method that you want to call
   https://localhost:5001/api/hangfire/databaseupdate

   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/0fec4425-978d-4c4b-81cc-8c169540a63d)


   Open the Hangfire dashbaord, now you can see the DatabaseUpdate job in the Recuuring Jobs Tab in the top panel.

   It will give details on when the job was created, last execution, next execution.
  
   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/b65811c5-5f5d-4e72-92f5-2b076ce26d7c)


   Once the job is execxuted (i.e, after a minute as per its implementation) , it will also be reflected in the succeeded tab.
       
   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/f3d982d7-f611-46f1-ae65-71b4c0863257)

   ![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/00373d9c-94ba-4617-95cc-b94247f7a276)

   JobId 7 is the 1st triggered recurring job.
   If you check after couple minutes it will have Jobs with ID 8,9,10 executed for every minute since Cron job is scheduled minutely.

   (before proceeding to next job make sure, you delete the recurring job)

   ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

4.  *CONTINUOUS JOBS*

    "This Job is meant to be used when you have chain reaction of events"

    eg, User decides to unsubscribe from your app, once the user does that you want to send them confirmation email.
    Therefore, the second job here i.e, 'Sending a confirmation email' waits for the 1st job to be called that is 'Unsubscription'.
    Here 'Unsubscribe' is the action that happens.

     > BackgroundJob.ContinueJobWith(parentJobId, () => Console.WriteLine("You were unsubscribed!"));    // this is 2nd job, i.e, sending confirmation

     the job will be executed only if we have a parent job i.e, unsubscribe in this case

      Code snippet:
   >
    
       [HttpPost]
        [Route("[action]")]
        public IActionResult Confirm()
        {
            int timeInSeconds = 30;
            var parentJobId = BackgroundJob.Schedule(() => Console.WriteLine("You asked to be unsubscribed!"), TimeSpan.FromSeconds(timeInSeconds));

            BackgroundJob.ContinueJobWith(parentJobId, () => Console.WriteLine("You were unsubscribed!"));

            return Ok("Confirmation job created!");
        }

Once the below job is executed,
var parentJobId = BackgroundJob.Schedule(() => Console.WriteLine("You asked to be unsubscribed!"), TimeSpan.FromSeconds(timeInSeconds));

the second job is executed in 30 seconds because we've defined int timeInSeconds = 30;


Now we use POSTMAN to send a POST Request

 Make a POST request call, paste the url followed by Action method that you want to call
 https://localhost:5001/api/hangfire/confirm
 
![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/5f306889-56ff-48ca-833a-7a8cf9e4c730)

  Open the Hangfire dashbaord, now you can see the parentjob in Scheduled Job and 1 Awaiting job which is the second confirmation job

![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/101cb8bd-0e86-4bd5-9901-7e01c68731ae)


  If you click on the jobId of Awaiting job, you can see on which parent job is this awaiting
![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/564f5c1a-03ba-418a-bf98-3925b73c99d2)


  And if you click on the Parent JobId, you can see that this job is scheduled.

![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/0875a64f-fce5-4c82-a620-707f3d9beca5)

  Finally, you can check the Succeeded job tabs to check if both job have been executed successfully.

![image](https://github.com/RachReddy/Hangfire--Background-Processing/assets/94166047/db367342-2a19-4e7b-8dbb-d42f32daad72)
    
