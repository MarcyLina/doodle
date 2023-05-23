If you're a developer who loves using Twig templates and wants to integrate them into ExpressionEngine, Laravel Coilpack is a fantastic tool to consider. Coilpack enables the use of your Twig templates in ExpressionEngine, making it possible to create stunning, customized websites more easily than ever before.

In this blog post, we'll explore some of the key benefits of using Coilpack to integrate your Twig templates into ExpressionEngine, as well as provide a step-by-step guide to getting started.

### Benefits of Using Twig with ExpressionEngine

1. Twig is a powerful and widely adopted templating engine for web development that has gained popularity due to its simplicity, flexibility, and developer-friendly features. Twig is used in popular web development frameworks like Symfony, Drupal, and Laravel, among others, and has become a standard for many modern web projects.
2. Twig offers **wide IDE (Integrated Development Environment) support,** with plugins and extensions available for popular IDEs such as Visual Studio Code, PhpStorm, Sublime Text, and Atom. These plugins provide features like syntax highlighting, auto-completion, code snippets, error highlighting, and debugging tools, making it easier for developers to work with Twig templates.

### Getting Started with Laravel Coilpack

**Step 1: Create a new Laravel Application**
The first step is to create a new Laravel project. Firstly, make sure you have Composer installed on your system. Then, open up a terminal and navigate to the directory where you want to create the new Laravel project. Next, run the following command:

	composer create-project --prefer-dist laravel/laravel doodle

This will download the latest version of Laravel and all its dependencies. Once the installation is complete, navigate to the project directory using:

	cd doodle

 and run the following command to start the development server:

	php artisan serve

Now, you can access your new Laravel application by opening a web browser and navigating to `http://localhost:8000.`

**Step 2: Set up Coilpack**
Installing Coilpack is remarkably similar to installing Breeze. Require the package, and run its installation command:

	composer require expressionengine/coilpack:1.x && php artisan coilpack

This command will ask you a few questions:

	Would you like to install ExpressionEngine or choose an existing installation [install]:[0] install[1] choose

  ![Express Engine Admin Page](/assets/images/articles/coilpack-installation.png)

For this tutorial, we’re creating a brand new ExpressEngine install for our Laravel app, so let’s choose the first option. However, if you’re adding Laravel to an existing EE application, choose the second option and point Coilpack to your existing app.

Coilpack will now automatically download and install ExpressionEngine into an `ee` folder in your Laravel app—e.g. `~/Sites/doodle/ee`.

Finally, let's ignore a few directories in the ee folder. Add the following two lines to the `.gitignore` file at the root of the Laravel application:

	ee/system/user/cache/*ee/system/user/logs/*

Let’s do a Git commit, so we can add the new Composer dependency, the `config/coilpack.php` config file, and the entire `ee/` directory: `git add && .git commit -m "Install Coilpack."`

**Step 3: Install ExpressionEngine**
Now, let’s visit ExpressionEngine’s admin page to finish configuring our new install. If you’re using a tool like Laravel Valet, that URL will be http://doodle.test/admin.php.
Before finishing the ExpressionEngine install, make sure you’ve created a database for your app.

![Express Engine Admin Page](/assets/images/articles/adding-ee-to-laravel-configure-admin-page.png)

The default database settings ExpressEngine ships with are correct for many local database installs, so you may only need to fill out the `Administrator Account` section and then you’ll be good to go. You can leave "Install default theme?" unchecked for this tutorial.

Note to Valet users: Check to make sure that your `APP_URL` in `.env` is not set to `http://localhost`, but rather to the URL you are using locally. For me, that URL is `http://doodle.test`. Otherwise, your styles will not compile.

Once you’ve filled that form out, visit `http://doodle.test` in the browser, and you should see a notice indicating that ExpressionEngine was installed correctly. Great! Now, we can move forward with setting up our site—but first, let’s commit the file changes we just made: `git add . && git commit -m "Install EE via Coilpack."`

**Step 4: Configure Twig Templates**
To begin using Twig templates in your ExpressionEngine site, create a new file in your code editor at `/ee/system/user/templates/default_site/example.group/index.html.twig`. Copy the code snippet provided below and paste it into the new file.

	/ee/system/user/templates/default_site/example.group/index.html.twig

```
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{{ global.site_name }}</title>

    <script src="https://cdn.tailwindcss.com"></script>
  </head>
  <body>
	<div class="mx-auto max-w-7xl text-center px-4 sm:px-6 lg:px-8">
		<div class="mx-auto max-w-4xl">
			<h1 class="text-3xl py-8 capitalize">Welcome to {{ global.site_name }}!</h1>
		</div>
	</div>
  </body>
</html>
```

From here you should be able to view your page at `your-site-url.test/example`

-----screenshot-----

Now let's create another Twig template to render blog posts here:

	/ee/system/user/templates/default_site/_partials/blog.html

Add this code to the new blog template:

```
<div>
    <h1 class="text-4xl font-bold text-center my-8">Blog</h1>
    {% set blogEntries = exp.channel.entries({channel:'blog', status: 'open'}) %}

    <div class="grid grid-cols-3 gap-3">
        {%- for entry in blogEntries -%}
        <div class="bg-gray-100 rounded-lg h-40 p-6">
            <h3 class="text-2xl">
                {{ entry.title }}
            </h3>
            <p>{{ entry.description }}</p>
        </div>
        {% else %}
            <p class="col-span-3">Sorry, no blog entries were found.</p>
        {% endfor %}
    </div>
</div>
```

After that, add the new  partial `{% include 'ee::_partials/blog' %}` to the body tag in `/ee/system/user/templates/default_site/example.group/index.html.twig` It should now look like this:

```
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{{ global.site_name }}</title>

    <script src="https://cdn.tailwindcss.com"></script>
  </head>
  <body>
	<div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
		<div class="mx-auto max-w-4xl">
			<h1 class="text-3xl py-8 capitalize">Welcome to {{ global.site_name }}!</h1>
			{% include 'ee::_partials/blog' %}
		</div>
	</div>
  </body>
</html>
```

If you naviagte back to `your-site-url.test/example`, you can see the blog output
----screenshot---

Now go ahead and add some content to see the final rendering
----screenshot-------


<!-- And lastly for the FAQs

/ee/system/user/templates/default_site/_partials/faqs.html

```
<div>
    <h1 class="text-4xl font-bold my-8">FAQs</h1>
    {% set faqEntries = exp.channel.entries({channel:'faqs', status: 'open'}) %}

    <div class="grid grid-cols-3 gap-3">
        {%- for entry in faqEntries -%}
        <div class="bg-gray-100 rounded-lg h-40 p-6">
            <h3 class="text-2xl">
                {{ entry.title }}
            </h3>
            <p>{{ entry.description }}</p>
        </div>
        {% else %}
            Sorry, no FAQs were found.
        {% endfor %}
    </div>
</div>
``` -->
