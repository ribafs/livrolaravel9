laravel new ckeditor

cd ckeditor

Criar o banco e conf .env

Criar model e migration

php artisan make:model Post -m

Editar a migration criada e atualizar

    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->text('body');
        $table->timestamps();
    });
    
php artisan migrate

Editar o model e atualizar

<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
 
class Post extends Model
{
    use HasFactory;
     
    protected $fillable = [
        "title",
        "body"
    ];
}

Atualizar as rotas

<?php
use App\Http\Controllers\PostController;
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('welcome');
});

Route::get('posts', [PostController::class, "index"]);
Route::get("create", [PostController::class, "create"]);
Route::post('store', [PostController::class, "store"]);

Criar o controller

php artisan make:controller PostController

<?php
 
namespace App\Http\Controllers;
 
use Illuminate\Http\Request;
use App\Models\Post;
 
class PostController extends Controller
{
     
    public function index() { 
        $posts =  Post::orderBy("id", "desc")->paginate(5);
        return view("posts", compact("posts"));
    }
 
    // Create Post 
    public function create() {
 
        return view("create");
    }
 
 
    public function store(Request $request) {
 
        $post = [  "title"  =>  $request->title,
                    "body" => $request->body
                ];
       
        $post  =  Post::create($post);
 
        return back()->with("success", "Post has been created");
     
    }
}

Criar as views em

resources/views

posts.blade.php

<!doctype html>
<html lang="en">
<head>
<title> Laravel 9 Posts List For CKEditor Example </title>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
<!-- Bootstrap CSS -->
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
<style>
table td p {
word-break: break-all;
}
</style>
</head>
<body>
<div class="container mt-4">
<div class="row">
<div class="col-xl-8">
<h3 class="text-right"> Laravel 9 CKEditor Integration Example</h3>
</div>
<div class="col-xl-4 text-right">
<a href="{{url('create')}}" class="btn btn-primary"> Add Post </a>
</div>
</div>
@if(count($posts) > 0)
<div class="table-responsive mt-4">
<table class="table table-striped">
<thead>
<tr>
<th> Id </th>
<th style="width:30%;"> Title </th>
<th> Decription </th>
</tr>
</thead>
<tbody>
@foreach($posts as $post) 
<tr>
<td> {{ $post->id }} </td>
<td> {{ $post->title }} </td>
<td> {!! html_entity_decode($post->body) !!} </td>
</tr>
@endforeach
</tbody>
</table>
</div>
{{ $posts->links() }}
@endif
</div>
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
</body>
</html>

create.blade.php

<!doctype html>
<html lang="en">
<head>
<title> Laravel 9 Install CKEditor Example </title>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
{{-- CKEditor CDN --}}
<script src="https://cdn.ckeditor.com/ckeditor5/23.0.0/classic/ckeditor.js"></script>
</head>
<body>
<div class="container mt-5">
<div class="row">
<div class="col-xl-12 text-right">
<a href="{{ url('posts') }}" class="btn btn-danger"> Back </a>
</div>
</div>
<form action="{{url('store')}}" method="POST">
@csrf
<div class="row">
<div class="col-xl-8 col-lg-8 col-sm-12 col-12 m-auto">
@if(Session::has('success'))
<div class="alert alert-success alert-dismissible">
<button type="button" class="close" data-dismiss="alert">×</button>
{{ Session::get('success') }}
</div>
@elseif(Session::has('failed'))
<div class="alert alert-danger alert-dismissible">
<button type="button" class="close" data-dismiss="alert">×</button>
{{ Session::get('failed') }}
</div>
@endif
<div class="card shadow">
<div class="card-header">
<h4 class="card-title"> Laravel 9 Install CKEditor Example Tutorial </h4>
</div>
<div class="card-body">
<div class="form-group">
<label> Title </label>
<input type="text" class="form-control" name="title" placeholder="Enter the Title">
</div>
<div class="form-group">
<label> Body </label>
<textarea class="form-control" id="body" placeholder="Enter the Description" name="body"></textarea>
</div>
</div>
<div class="card-footer"> 
<button type="submit" class="btn btn-success"> Save </button>
</div>
</div>
</div>
</div>
</form>
</div>
<script>
ClassicEditor
.create( document.querySelector( '#body' ) )
.catch( error => {
console.error( error );
} );
</script>
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
</body>
</html>

php artisan serve

http://127.0.0.1:8000/create

Experimente entrar algumas informações e veja que ao clicar em submit elas vão para o banco.

https://www.tutsmake.com/how-to-install-and-use-ckeditor-in-laravel-9/
