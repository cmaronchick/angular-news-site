# angular-news-site

That should get you excited, shouldn’t it? Let’s start by building the app.

Prerequisites
This is what you’re going to need in order to follow along with this tutorial:

Node.js and npm installed on your machine;
Angular CLI installed on your machine;
A basic understanding of Angular.
Once that stuff is out of the way, we can proceed.

Setting Up The Angular Project
In this section, we’re going to use the Angular command line interface (CLI) to generate a new Angular project. To do so, head over to the CLI and run this:

ng new news-appCopy
Next, point your command line to the project’s root folder by running the following:

cd news-appCopy

Installing Dependencies
To set up our dependencies, we’re going to install, with just one command, all of the dependencies necessary for this tutorial. Don’t worry, I’ll explain this in a second:

npm install --save @angular/material @angular/animations @angular/cdkCopy
We have three packages being installed with this command.

@ANGULAR/MATERIAL
This is the official material design package for the Angular framework.

@ANGULAR/ANIMATIONS
Installing the Angular animation package separately from the Angular core library is necessary. Certain material components need access to the animation libraries, which is why we’re installing it here.

@ANGULAR/CDK
The CDK part stands for “component dev kit”, which provides us with high-quality predefined behaviors for your components, since modern web development is all about components.

It is recommended to include the Angular CDK any time you want to link Google’s material design to an Angular application.

To find out more about Angular CDK, check out this article.

Let’s run our app to see that everything works just fine. You can start a development server by running the following command:

ng serveCopy
Now, if you visit http://localhost:4200/ in a browser, you should see the following page:

Welcome to news app
Running the Angular project on development server. (Large preview)
Now, in your code editor, navigate to the file /src/app/app.module.ts, and add the following packages that we’ve just installed:

… Other imports …
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { MatButtonModule, MatCardModule, MatMenuModule, MatToolbarModule, MatIconModule, MatSidenavModule, MatListModule } from '@angular/material';
Copy
It is important to understand what’s going on here. First, we’re importing the animations package to animate our application a bit.

The next import is what’s unique to Angular material. Before, we just included a single material module. Now, we have to import each material component that we intend to use.
As you can see, we’ve added seven different modules here for material buttons, cards, menus, lists toolbars, side navigation, and icons.

After adding those packages to your app.module.ts file, make sure that your file matches the following:

import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule } from '@angular/common/http';
import { NewsApiService } from './news-api.service';

import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { MatButtonModule, MatCardModule, MatMenuModule, MatToolbarModule, MatIconModule, MatSidenavModule, MatListModule } from '@angular/material';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    HttpClientModule,
    MatButtonModule,
    MatMenuModule,
    MatCardModule,
    MatToolbarModule,
    MatIconModule,
    MatSidenavModule,
    MatListModule,
  ],
  providers: [NewsApiService],
  bootstrap: [AppComponent]
})
export class AppModule { }
Copy
Note: The statement import { HttpClientModule } from @angular/common/http in the file above wasn’t generated automatically, but rather added manually. So, make sure you do that, too. And don’t worry about the NewsApiService service provider because we’re going to take care of that later on.

You might be wondering, though, how did I know the names of the modules to import? The official Angular material documentation gives you the exact code needed to import each module.

If you click on any of the components in the left menu and then click on the “API” tab, it provides you with the exact import line that you need to use.

API reference for Angular Material Components
API reference for Angular Material Component. (Large preview)
In terms of setup, that’s all we need to do before we actually begin using and integrating material components in our templates.

You just have to remember to import each unique component that you plan to use.

Acquiring Free API Key
We’re going to use the News API to feed us some news headlines as JSON data, which we’ll implement in the application template.

What is the News API service?

The News API is a simple HTTP REST API for searching and retrieving live articles from all over the web.

Now that you know what the News API is, the next step is to get a free API Key, which will help us make some call requests to the server and grab the news articles.

You can sign up for just 30 seconds. You’ll only need to provide your first name, email address, and password. That’s all.

After signing up, you’ll find the API key already generated for you in the dashboard. Just save it in a text file somewhere on your desktop; because we’ll use it in the next chapter.

Working On The Components
To start working on the components, you need to create a service provider to manage the interaction with the News API service.

CREATING THE SERVICE PROVIDER
Enter this command to generate a new service provider:

ng generate service NewsApi
Copy
After that, go to the generated /src/app/news-api.service.ts file, and add the following code to it:

import { Injectable } from '@angular/core';
import { HttpClient  } from '@angular/common/http';


@Injectable({
  providedIn: 'root'
})
export class NewsApiService {

  api_key = 'PUT_YOUR_API_KEY_HERE';

  constructor(private http:HttpClient) { }
  initSources(){
     return this.http.get('https://newsapi.org/v2/sources?language=en&apiKey='+this.api_key);
  }
  initArticles(){
   return this.http.get('https://newsapi.org/v2/top-headlines?sources=techcrunch&apiKey='+this.api_key);
  }
  getArticlesByID(source: String){
   return this.http.get('https://newsapi.org/v2/top-headlines?sources='+source+'&apiKey='+this.api_key);
  }
} 
Copy
It’s time to use our API Key. Just paste it where it says, “Put_YOUR_API_KEY_HERE”.

We’ve imported HttpClient, which will be responsible for making API calls to our endpoints and fetching news headlines for us.

Now, for the initSources function, we simply prepare our left-side menu with some news resources. After that, we’ve created another function, initArticles which retrieves the first articles from TechCrunch once the application gets started.

As for the last function, getArticlesByID, it’s going to simply bring some articles for the passing parameter.

THE MAIN COMPONENT
The service provider is done. Let’s move to the /src/app/app.component.ts file and add this code:

import { Component } from '@angular/core';
import { NewsApiService } from './news-api.service';


@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {

mArticles:Array;
mSources:Array;
    
constructor(private newsapi:NewsApiService){}

ngOnInit() {
        //load articles
       this.newsapi.initArticles().subscribe(data => this.mArticles = data['articles']);
      //load news resources
     this.newsapi.initSources().subscribe(data=> this.mSources = data['sources']);    
}

searchArticles(source){
     this.newsapi.getArticlesByID(source).subscribe(data => this.mArticles = data['articles']);
}
}
Copy
We’re defining two properties here: mArticles, for holding news articles, and mSources, for holding news resources. Both are defined as an array.

In the constructor, we’re simply creating a NewsAPIService instance.

Next, we’re using that instance on the ngOnInit() function to initialize our two properties.

For the searchArticles function, it will be triggered whenever the user selects a specific resource from the left-side menu. Then we’re passing this parameter to the getArticlesByID service provider function to retrieves articles for it.


MONDAY
Project tracking, teamwork & client reporting like you've never seen before. Start Your Free Trial Now.
START FREE TRIAL
Defining Material’s Default Style
In our /src/styles.css file, which is generated by the Angular CLI, let’s add the following:

@import '~@angular/material/prebuilt-themes/indigo-pink.css';
body {
    padding: 2em 23em;
    background:lightgray;
}
Copy
Based on your preference, you can change indigo-pink.css to:

deeppurple-amber.css
indigo-pink.css
pink-bluegrey.css
purple-green.css
I'm also adding some CSS to the body tag, only to demonstrate this layout. This helps it look more like an app, even on desktop.

Let’s also add two lines to our /src/index.html file just before the closing head tag:

<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700,400italic">
Copy
The first line imports the material design icon font, and the second one is the Roboto font, which is used by the material design team.

Defining A Template
Let’s start off by adding the following template in the /src/app/app.component.html file:

<mat-toolbar color="primary">
  <button mat-button (click)="sidenav.open ()" ><mat-icon>menu</mat-icon></button>
  <span>News Headlines</span>  
  <span class="example-spacer"></span>
  <button mat-button [matMenuTriggerFor]="appMenu"><mat-icon>settings</mat-icon></button>
</mat-toolbar>
<mat-menu #appMenu="matMenu">
  <button mat-menu-item> Settings </button>
  <button mat-menu-item> Help </button>
</mat-menu>
<mat-sidenav-container class="example-container">
  
  <mat-sidenav #sidenav class="example-sidenav">
    <mat-list class="list-nav">
        <mat-list-item class="list-item" *ngFor="let source of mSources" (click)="searchArticles(source.id);sidenav.close();">
          
          <div mat-card-avatar [ngStyle]="{'background-image': 'url(../assets/images/'+ source.id +'.png)'}" class="example-header-image"></div>
          
          <span class="source-name"> {{source.name}}</span>
        
        </mat-list-item>
    </mat-list>
  </mat-sidenav>
  <mat-card class="example-card"  *ngFor="let article of mArticles">
    <mat-card-header>
      <div mat-card-avatar [ngStyle]="{'background-image': 'url(../assets/images/'+ article.source.id +'.png)'}" class="example-header-image"></div>
      <mat-card-title class="title">{{article.title}}</mat-card-title>
      <mat-card-subtitle>{{article.source.name}}</mat-card-subtitle>
    </mat-card-header>
    <img mat-card-image class="img-article" src={{article.urlToImage}} alt="">
    <mat-card-content>
      <p>
        {{article.description}}
      </p>
    </mat-card-content>
    <mat-card-actions class="action-buttons">
      <button mat-button color="primary"><mat-icon>thumb_up_alt</mat-icon> 12 Likes</button>
      <button mat-button color="primary"><mat-icon>comment</mat-icon> Comments</button>
      <button mat-button color="primary"><mat-icon>share</mat-icon> Share</button>
      <a mat-button color="primary" href={{article.url}} target="_blank" ><mat-icon>visibility</mat-icon> More</a>
    </mat-card-actions>
  </mat-card>
</mat-sidenav-container>
Copy
So, what have we done here?

First, we define a toolbar with a left-side menu, along with the application’s main title and the settings’ right menu.

Next, we’re using *ngFor for both sources and articles, and in doing so, our left-side menu will hold the news resources, and the main contents will hold the news articles.

One thing to notice is that on the click event of our list items, we’ve added two functions because that event executes any JavaScript code. The first function is searchArticles, which we’ve already explain, and the second one is sidenav.close() which will automatically close our left-side menu once the user has selected a resource.

STYLING OUR COMPONENT
The last thing to do with the components is to visit the /src/app.component.css file and paste the following code in it:

.example-spacer {
  flex: 1 1 auto;
}

.example-card{
    margin-top: 4px;
}

.example-header-image { 
  background-size: cover;
}

.title{
    font-weight: bold;
}

.img-article{
    height: 350px;
}

.action-buttons{
    text-align: center;
}
    
.example-container {
    width: 100%;
    height: auto;
    border: 1px solid rgba(111, 111, 111, 0.50);
}
  
.example-sidenav-content {
    display: flex;
    height: 75%;
    align-items: center;
    justify-content: center;
}
  
.example-sidenav {
    padding: 20px;
}

.source-name {
    margin-left:5px; 
}

.list-item:hover{
    cursor: pointer;
    background-color: #3f51b5;
    color: white;
}
Copy
SET UP IMAGES FOR NEWS RESOURCES
Move to the /src/assets directory, and create a new folder named images. Then, download these images either from a Google Drive link or the GitHub repository.

They are the logos of our news resources. Once you download them, copy and paste all of the image files into the images folder that you just created.

Once everything is complete, run this:

ng serveCopy
Now, your app should look like the screenshot below. Pretty awesome, huh!

the news application final product
Launching the app after everything is complete. (Large preview)
Note that when the news snippets are loaded on the main page, a “More” button (as you can see in the picture above) takes the user to read the whole story.

Conclusion
There you have it! I hope this tutorial was useful and that you’ve enjoyed building this application. If so, feel free to leave your feedback and comments below. In the meantime, the Angular Material documentation is pretty cool. It provides you with an overview of each component, an API and an example.

The entire source code of this app is available on GitHub. You can also check out this website, which already implements the service API used in this tutorial.
