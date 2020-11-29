# MVC Live Project

### Restrict Access to Productions Delete Page
If a User appends /Delete/#, where # is the ID of one of the Productions, the User gets taken to that Production's Delete page.  This means that a random User, if they are able to guess a valid ID, is currently able to delete Productions.  Fix this issue by restricting access to the Delete page so that only Users signed in as Admin can access the page.

Solution: 
In Controllers/ProductionsController.cs

Add Data Annotation to GET form of Delete()

```
[Authorize(Roles = "Admin")]
public ActionResult Delete(int? id)
{
	...
}
```

---

### Production Details - Consolidate Links
At the bottom of the Production Details page, if you log in as an admin, you'll notice that a link "Edit | " appears on its own line.  That Edit link is supposed to be on the same line as the other links, like this,

`"Edit | Current Productions | Back to List"`

Please consolidate these links into a single line.  When you log out as an admin, "Edit | " should gracefully disappear.
```
// "@:" renders rest of line as HTML. 
// https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-3.1#razor-code-blocks
<p>
  @if (ViewContext.HttpContext.User.IsInRole("Admin"))
  {
    @Html.ActionLink("Edit", "Edit", new { id = Model.ProductionId }) @:|
  }
    @Html.ActionLink("Current Productions", "Current") |
    @Html.ActionLink("Back to List", "Index")
</p>
```

---

### Fix Duplicating Awards Seeding
When you run the project, the SeedAwards method in the Startup file seeds the database with Awards.  However, if you run the project again, those same Awards are added to the database again.  Find out why the Awards are being duplicated every time the project is run and implement your solution.

\*\*\*Please reach out for any clarification
```
/* 
 * awards.ForEach(award => context.Awards.AddOrUpdate(aw => aw.AwardId, award));
 * 
 * This line of code produces duplicate seed records every time the program is run. 
 * This is due to the use of AwardId in AddOrUpdate(). When an Award object is instantiated, 
 * AwardId is not yet known because its value is set by the DB upon insertion. 
 * Hence, the object will always not be found, and therefore will always be added. 
 * Whereas, in the other seed methods the calls to AddOrUpdate() refer to a table column 
 * that acts as an alternate key that uniquely identifies the record. In the Awards table 
 * the compound key (Year, Name, Type, Category) serves as an alternate key.
 */
awards.ForEach(award => context.Awards.AddOrUpdate(a => new { a.Year, a.Name, a.Type, a.Category }, award));
```
---

6914 Production Index - Production Ribbons

For this story you will be adding red ribbons to the production images on the Production Index page, similar to the ones on the home page, except for the fact that they will be located on the lower right corner of an image, rather than on the upper right corner.  For the production that is currently playing, the ribbon will say "Onstage".  For the productions that will play in the future, their ribbons will say "Coming Soon".  For all other productions the ribbons should not be visible.  You can make the ribbon background color slightly transparent.  The result will look like this:

<div class="prod-index-ribbon-parent">
<a href="@Url.Action("Details", "Productions", new { id = item.ProductionId })">
  <img class="card-img-top production-index-img bg-black" src="@Url.Action("DisplayPhoto", "Photo", new { id = item.DefaultPhoto.PhotoId })" alt="Card image cap">
  <div class="prod-index-ribbon">
	@if (item.IsCurrent)
	{
	  @: Onstage
	}
	else if (item.OpeningDay > DateTime.Now)
	{
	  @: Coming Soon
	}
  </div>
</a>
</div>

/* BEGIN Production Index ribbon styles */
.prod-index-ribbon-parent {
    position: relative;
    overflow: hidden;
}
.prod-index-ribbon {
    position: absolute;
    opacity: 0.8;
    width: 10em;
    text-align: center;
    background-color: var(--main-bg-color);
    color: var(--light-color);
    transform-origin: top left;
    transform: rotate(-45deg);
    /* right = -width * (1 - cos(45deg))
       If width is changed, right must be recalculated */
    right: -3em;
    top: 100%;
}
/* END Production Index Ribbon styles */

============

6915 Enhance Photo Delete Page

Currently the Photo Delete page is still the basic page that was scaffolded by MVC.  It is quite plain and has no visual spacing on the left or on the top.  Please enhance the UI of this page.  You have the freedom to do this according to the best of your judgment and design sense.

Some suggestions for you to think about:

    There is no need for the title "Delete"
    There is no reason why "Are you sure you want to delete this?" and "Photo" are 2 separate sentences
    OriginalHeight and OriginalWidth can be displayed as individual fields like they are currently, or simply displayed as dimensions, e.g. 1200 x 1800
    If there is no value for Title then it can be hidden entirely.  The Title can also be positioned above the image.
    It is technically not necessary to display all the property names "PhotoFile", "Title", etc.
    You may choose to center the information on this page
    The Delete button currently stretches from edge to edge, which is a very unusual design choice.  
    You might also find it preferable to replace the green color of the button with one of our main website colors
    As mentioned earlier, there should be padding/margin around the main content on the page.

You can also add other features/improvements that you think would enhance the visual appeal of this page.

==============
