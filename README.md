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



   
   
