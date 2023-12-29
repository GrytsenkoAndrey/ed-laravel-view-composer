# ed-laravel-view-composer
How to Use View Composer in Laravel

View Composer helps adding a common view to other view files without repettedly adding same view code to different view files.

Benifits of ssing View Composer:

- It removes the bad practice of writting logical code inside view file as we write logics sepeartely in a php file.
- It allows writing a view file re-usueable inside other view files.

Let’s see an example.

## Step:1

Imagine We have a 2 different view pages and we want to show blog posts in both files. Instead of writing blog posts code in both file we can create a viewcomposer for the blog posts and include in both view pages.

Let’s see how we can implement this.

First, Create a folder inside App/Http folder as ViewComposers and create a php file called PostComposer.

```
<?php

namespace App\Http\ViewComposers;

use App\Models\Post;
use Illuminate\View\View;

class PostComposer
{
    /**
     * The post model implementation.
     *
     * @var Post
     */
    protected $post;

    /**
     * Create a new post composer.
     *
     * @param  Post  $post
     * @return void
     */
    public function __construct(Post $post)
    {
        $this->post = $post;
    }

    /**
     * Bind data to the view.
     *
     * @param  View  $view
     * @return void
     */
    public function compose(View $view)
    {
        $posts = $this->post->latest()->take(5)->get();

        $view->with('posts', $posts);
    }
}
```

## Step2:

Inside AppServiceProvider, Add PostComposer Class first:

```
use App\Http\ViewComposers\PostComposer;
```

Load PostComposer inside boot function:

```
public function boot()
{
    // path of blog folder
    $this->loadViewsFrom(\resource_path('views/blog'), 'blog');

    // Load view composer
    View::composer('blog::posts', PostComposer::class);
}
```

## Step 3:

Create view for posts inside posts.blade.php:

```
@if ($posts)
    <div>
        <h3>Posts</h3>

        @foreach ($posts as $post)
            <div>
                <h5>{{ $post->title }}</h5>
                <p>{{ $post->description }}</p>
                <small>{{ $post->created_at->format('d-m-Y') }}</small>
            </div>
        @endforeach
    </div>
@else
  No posts available
@endif
```

Now include the posts in any view blade file and it will show the posts.

```
@include('blog::posts')
```

