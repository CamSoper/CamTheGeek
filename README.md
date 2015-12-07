# CamTheGeek
This is my general utilities class library.  Right now, there's only the one class in the library.  Will add more as they come up.  I welcome contributions.

## GenericWebApiClient Usage

This is a class to make calling a scaffolded ASP.NET Web API controller extremely simple.  Just instantiate it with the type of object your Web API scaffolded controller uses, like this:

`GenericWebApiClient<Movie> movieservice = new GenericWebApiClient<Movie>(new Url("http://mywebapi.com/movies")`

Then you'll be able to do all the standard, off-the-shelf scaffolded operations.

### Get All
`var m = movieservice.GetAll();`

### Get by ID
`var m = movieservice.GetById(id);`

### Create
`movieservice.Create(movie);`

### Edit
`movieservice.Edit(movie, movie.ID);`

### Delete
`movieservice.Delete(id);`

## Longer example

Here's an example of an MVC controller I wrote that leverages this class.

```
public class MoviesController : Controller
{
    GenericWebApiClient<Movie> movieservice;

    public MoviesController()
    {
        string ServiceBaseUri = ConfigurationManager.AppSettings["MovieServiceBaseUrl"];

        if(string.IsNullOrEmpty(ServiceBaseUri))
        {
            throw new ConfigurationErrorsException("The 'MovieServiceBaseUrl' setting is incorrect.");
        }

        movieservice = new GenericWebApiClient<Movie>(new Uri(ServiceBaseUri));
    }

    // GET: /Movies/
    public ActionResult Index()
    {
            var movies = movieservice.GetAll();
            return View(movies);
    }

    // GET: /Movies/Details/5
    public ActionResult Details(int? id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Movie movie = movieservice.GetById(id);
        if (movie == null)
        {
            return HttpNotFound();
        }
        return View(movie);
    }


    public ActionResult Create()
    {
        return View();
    }

    // POST: /Movies/Create
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Title,ReleaseDate,Genre,Price,Rating")] Movie movie)
    {
        if (ModelState.IsValid)
        {
          movieservice.Create(movie);
          return RedirectToAction("Index");
        }

        return View(movie);
    }

    // GET: /Movies/Edit/5
    public ActionResult Edit(int? id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Movie movie = movieservice.GetById(id);
        if (movie == null)
        {
            return HttpNotFound();
        }
        return View(movie);
    }

    // POST: /Movies/Edit/5
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Title,ReleaseDate,Genre,Price,Rating")] Movie movie)
    {
        if (ModelState.IsValid)
        {
            movieservice.Edit(movie, movie.ID);
            return RedirectToAction("Index");
        }
        return View(movie);
    }

    // GET: /Movies/Delete/5
    public ActionResult Delete(int? id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Movie movie = movieservice.GetById(id);
        

        if (movie == null)
        {
            return HttpNotFound();
        }
        return View(movie);
    }

    // POST: /Movies/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        movieservice.Delete(id);
        return RedirectToAction("Index");
    }

    protected override void Dispose(bool disposing)
    {
        if (disposing)
        {
            movieservice.Dispose();
        }
        base.Dispose(disposing);
    }

}

```

