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
