### Real-Life Project: Multi-Page Blog Website with Authentication and CRUD Operations

---

**Project Overview:**  
Create a basic blog website with the following features:
- **Public Pages:** Home, About, and Contact pages accessible to all visitors.
- **Admin Section:** Blog management section where authenticated users can create, update, and delete blog posts.
- **Protected Routes:** Only logged-in users can access the blog management features.

This project will cover routing, controllers, middleware, and CRUD operations in Laravel, giving students a practical understanding of Laravel basics.

---

#### **Project Setup**
1. **Initialize a New Laravel Project:**
   ```bash
   laravel new BlogProject
   ```
   - Set up your `.env` file with database credentials.
   - Run migrations to set up the default user table:
     ```bash
     php artisan migrate
     ```

2. **Authentication Setup (Using Laravel Breeze or Laravel UI):**
   - Install Laravel Breeze for basic authentication:
     ```bash
     composer require laravel/breeze --dev
     php artisan breeze:install
     npm install && npm run dev
     php artisan migrate
     ```
   - This will generate registration, login, and password reset functionality.

---

#### **1. Create Public Pages (Home, About, Contact)**

1. **Define Routes:**
   - Open `routes/web.php` and define the routes for these pages:
     ```php
     Route::view('/', 'home')->name('home');
     Route::view('/about', 'about')->name('about');
     Route::view('/contact', 'contact')->name('contact');
     ```

2. **Create Basic Views:**
   - In `resources/views/`, create `home.blade.php`, `about.blade.php`, and `contact.blade.php`.
   - Add some sample content for each page.

---

#### **2. Build the Blog Section with CRUD Functionality**

1. **Create the Blog Controller and Model:**
   ```bash
   php artisan make:controller BlogController --resource
   php artisan make:model Blog -m
   ```
   - The `Blog` model will represent a blog post, and the `BlogController` will manage CRUD operations.

2. **Set Up Blog Database Table:**
   - Open the migration file in `database/migrations` for the `Blog` model and add fields like `title`, `content`, and `user_id`:
     ```php
     Schema::create('blogs', function (Blueprint $table) {
         $table->id();
         $table->string('title');
         $table->text('content');
         $table->foreignId('user_id')->constrained()->onDelete('cascade');
         $table->timestamps();
     });
     ```
   - Run the migration:
     ```bash
     php artisan migrate
     ```

3. **Define Blog Routes:**
   - Add routes for the Blog section in `web.php` and wrap them in the **auth** middleware:
     ```php
     Route::middleware(['auth'])->group(function () {
         Route::resource('blogs', BlogController::class);
     });
     ```

4. **Implement CRUD Methods in BlogController:**
   - Implement the `index`, `create`, `store`, `edit`, `update`, and `destroy` methods to handle blog operations.
   - Example of the `store` method:
     ```php
     public function store(Request $request)
     {
         $request->validate([
             'title' => 'required|max:255',
             'content' => 'required',
         ]);

         Blog::create([
             'title' => $request->title,
             'content' => $request->content,
             'user_id' => auth()->id(),
         ]);

         return redirect()->route('blogs.index')->with('success', 'Blog post created successfully.');
     }
     ```

---

#### **3. Apply Middleware for Route Protection**

1. **Restrict Access to Blog Management Routes:**
   - Ensure that only logged-in users can access the blog management section by using **auth** middleware on blog routes.

2. **Custom Middleware for Role-Based Access (Optional):**
   - For additional practice, create a custom middleware to allow only users with certain roles (e.g., admin) to access the blog management routes.

---

#### **4. Add Blade Views for Blog CRUD Operations**

1. **Create Blog Index Page:**
   - In `resources/views/blogs/index.blade.php`, list all blog posts with options to view, edit, or delete (if authorized).

2. **Create Blog Form Pages:**
   - For **Create** and **Edit** actions, add `create.blade.php` and `edit.blade.php` with a form to input `title` and `content`.

3. **Show Individual Blog Post:**
   - Add `show.blade.php` to display a single blog post.

---

#### **5. Testing the Project**

- **Test CRUD Functionality:** Add, edit, and delete blog posts, ensuring only authenticated users can access these features.
- **Test Middleware:** Verify that non-authenticated users cannot access the blog management routes.
