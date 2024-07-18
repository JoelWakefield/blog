# Project Management App Blog
To see the commit history of what was worked on, check out the [repo](https://github.com/JoelWakefield/ProjectManagementApp).

## 7/18/2024
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
