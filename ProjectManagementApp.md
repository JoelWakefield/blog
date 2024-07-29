# Project Management App Blog
To see the commit history of what was worked on, (or if you just want to see the source code) check out the [repo](https://github.com/JoelWakefield/ProjectManagementApp).

## 7/09/2024
### Updates
- The first day was setting up the initial environment and tools. I wanted to work on a project management app, something I've made in the past with Visual Basic, WinForms, SQL, and I believe .Net Framework 2.0 - 😵 (needless to say, it was painful to implement). The app worked fine, but I wanted to try for a version with more interactivity in terms of scheduling, assigning, and generally better quality of life features (both for the user and for the dev). This time around, the tech stack was a bit better:
  - Tech: .NET 8.0 (AspNetCore, EfCore), Blazor (w/MudBlazor), Postgres, Docker
  - Tools: Visual Studio, DBeaver, Docker Desktop
- I had help from [Wes Thurmond](https://github.com/jwthurmond), an old coworker and technological wizard. Wes suggested setting up a docker environment, and using pre-built values - anyone can run the app for themselves by following the instructions on the README.

## 7/17/2024
### Issues
- When using `AddIdentityCore`, it's important to call the `AddRoles` before `AddEntityFrameworkStores`.
```
builder.Services.AddIdentityCore<ApplicationUser>(options => options.SignIn.RequireConfirmedAccount = false)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>()
    .AddSignInManager()
    .AddDefaultTokenProviders();
```

## 7/18/2024
### Updates
- Migrations were added to account for the new Project type.
- The project builder was given a set of sample projects.
- A few pages for editing, reviewing, and creating projects were added.
- I updated the navbar icons - probably best that they aren't all the same. 😸
### Issues
- The first part of the day was getting the ef migrations to work. I first thought about creating multiple DBContexts to work with, but after [reading](https://stackoverflow.com/questions/11197754/entity-framework-one-database-multiple-dbcontexts-is-this-a-bad-idea#:~:text=I%20wrote%20this%20answer%20about%20four%20years%20ago%20and%20my%20opinion%20hasn%27t%20changed.%20But%20since%20then%20there%20have%20been%20significant%20developments%20on%20the%20micro%2Dservices%20front.%20I%20added%20micro%2Dservices%20specific%20notes%20at%20the%20end...) about someone else's issues, I decided against it. Instead, I chose to use one dbcontext; thankfully, when I tried to update the migrations, I was giving an error about needing to clarify which context to update - if it hadn't been for this error, I might have started down the painful path to the magical land of "painfully, needlessly complicated".
- The next issue was getting the postgres `timestanptz` working with the .NET `DateTime` - something which seems to plague others as well. It's a bit odd, but setting everything to Utc seems to do the trick - even weirder, the dates are still stored based on my utc adjustment, not in pure utc time. 💀
- The last issue I had was with the create a new project page (url: `/projects/create`) page. At first, it would show as a project details page (url: `/projects/{id}`). I realized that the `create` part was probably mistaken for an `id`, so I changed the details url to `/projects/details/{id}`. Then, the create a new project page showed a 404; after plugging around, it seems like some of the following modifiers were added to the `.csproj` file:
  ```
  <ItemGroup>
    <Content Remove="Components\Pages\Project\CreateProject.razor" />
  </ItemGroup>

  ...
  
  <ItemGroup>
    <UpToDateCheckInput Remove="Components\Pages\Project\CreateProject.razor" />
  </ItemGroup>

  <ItemGroup>
    <_ContentIncludedByDefault Remove="Components\Pages\Project\CreateProject.razor" />
  </ItemGroup>

  <ItemGroup>
    <None Include="Components\Pages\Project\CreateProject.razor" />
  </ItemGroup>
  ```
  I'm not certain why this occured for only this file (maybe the previous issue was enough to trigger these modifiers), but once I removed them, the create a new project page showed up.


## 7/29/2024
### Updates
- Implemented only services for the blazor pages - no injecting `DbContext`. I did this to create consistency (and a few uses of `DbContext` were reused across pages anyway.

### Issues
- I encountered issues trying to make asynchronous calls - a concurency problem with `DbContext`; there are many suggestions to fix this, but none of the suggested fixes have brought stability - only different issues. I decided the best approach was the one that worked - not calling anything `async`; now the original issues are solved.
- It seems that Blazor also has some issues with `.razor` pages having different lifetimes than registered services; I'm not certain if these issues are related to the concurency one - they _seem_ to be, but I'm not so certain. Again, while there are several solutions provided, they each create their own issues. There doesn't seem to be a "best practice", but others have acknoledged these issues (so I know I'm not crazy). 
