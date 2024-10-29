   using System.Collections.Generic;

   namespace CrowdfundingApp.Models
   {
       public class User
       {
           public int Id { get; set; }
           public string Username { get; set; }
           public string Email { get; set; }
           public string Password { get; set; }  // In a real application, use secure hashing for passwords
           public List<Project> Projects { get; set; }
       }
   }

   using System;

   namespace CrowdfundingApp.Models
   {
       public class Project
       {
           public int Id { get; set; }
           public string Title { get; set; }
           public string Description { get; set; }
           public decimal FundingGoal { get; set; }
           public decimal CurrentAmount { get; set; }
           public DateTime CreationDate { get; set; }
           public User Owner { get; set; }
           public int OwnerId { get; set; }
       }
   }

using Microsoft.EntityFrameworkCore;

namespace CrowdfundingApp.Models
{
    public class CrowdfundingContext : DbContext
    {
        public CrowdfundingContext(DbContextOptions<CrowdfundingContext> options) : base(options) { }

        public DbSet<User> Users { get; set; }
        public DbSet<Project> Projects { get; set; }
    }
}

{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=CrowdfundingDB;Trusted_Connection=True;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}


using CrowdfundingApp.Models;
using Microsoft.EntityFrameworkCore;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddDbContext<CrowdfundingContext>(options => 
            options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
        services.AddControllersWithViews();
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        // Existing code...
    }
}

using Microsoft.AspNetCore.Mvc;
using CrowdfundingApp.Models;

namespace CrowdfundingApp.Controllers
{
    public class UserController : Controller
    {
        private readonly CrowdfundingContext _context;

        public UserController(CrowdfundingContext context)
        {
            _context = context;
        }

        public IActionResult Register()
        {
            return View();
        }

        [HttpPost]
        public IActionResult Register(User user)
        {
            if (ModelState.IsValid)
            {
                _context.Users.Add(user);
                _context.SaveChanges();
                return RedirectToAction("Index", "Home");
            }
            return View(user);
        }
    }
}

using Microsoft.AspNetCore.Mvc;
using CrowdfundingApp.Models;
using System.Linq;

namespace CrowdfundingApp.Controllers
{
    public class ProjectController : Controller
    {
        private readonly CrowdfundingContext _context;

        public ProjectController(CrowdfundingContext context)
        {
            _context = context;
        }

        public IActionResult Index()
        {
            var projects = _context.Projects.ToList();
            return View(projects);
        }

        public IActionResult Create()
        {
            return View();
        }

        [HttpPost]
        public IActionResult Create(Project project)
        {
            if (ModelState.IsValid)
            {
                _context.Projects.Add(project);
                _context.SaveChanges();
                return RedirectToAction("Index");
            }
            return View(project);
        }
    }
}


   @model CrowdfundingApp.Models.User

   <h2>Register</h2>

   <form asp-action="Register" method="post">
       <div>
           <label asp-for="Username"></label>
           <input asp-for="Username" />
       </div>
       <div>
           <label asp-for="Email"></label>
           <input asp-for="Email" />
       </div>
       <div>
           <label asp-for="Password"></label>
           <input asp-for="Password" type="password" />
       </div>
       <button type="submit">Register</button>
   </form>


   @model CrowdfundingApp.Models.Project

   <h2>Create a Project</h2>

   <form asp-action="Create" method="post">
       <div>
           <label asp-for="Title"></label>
           <input asp-for="Title" />
       </div>
       <div>
           <label asp-for="Description"></label>
           <textarea asp-for="Description"></textarea>
       </div>
       <div>
           <label asp-for="FundingGoal"></label>
           <input asp-for="FundingGoal" />
       </div>
       <button type="submit">Create</button>
   </form>

   @model IEnumerable<CrowdfundingApp.Models.Project>

   <h2>Projects</h2>

   <a asp-controller="Project" asp-action="Create">Create New Project</a>

   <ul>
       @foreach (var project in Model)
       {
           <li>
               <h3>@project.Title</h3>
               <p>@project.Description</p>
               <p>Goal: @project.FundingGoal</p>
           </li>
       }
   </ul>

app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
});
