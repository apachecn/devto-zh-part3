# 使用 angular 在您的 web 应用程序中分享您的 Google 评论

> 原文：<https://dev.to/juanpablodelgado/share-your-google-reviews-in-your-web-app-with-angular-27cb>

### 有很多情况下你要分享客户的点评。在这种情况下，我会告诉你如何以简单的方式分享谷歌评论。

看起来是这样的:

[![](img/6cb23af0765049c08fec2070060ff66a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FqGKAgnh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t6vqmyuponde8tchggme.JPG)

或者你可以看看这个现场演示

## 第一步

### 前往你的 angular 项目中的 index.html，添加这个脚本

```
<script src="https://maps.googleapis.com/maps/api/js?v=3.exp&key= 
 <HERE_YOUR_GOOGLE_PRIVATE_API_KEY>&libraries=places">
</script>
// without <> 
```

#### 你可以从这里获得你的 Google Api 密匙 [GoogleApiKey](https://developers.google.com/maps/documentation/directions/get-api-key)

## 第二步

### 在项目中为您的评论创建一个组件

##### [T1】reviews . component . ts](#reviewscomponentts)

```
import { Component, AfterViewInit } from '@angular/core';
import { environment } from 'src/environments/environment';

declare const google: any;

@Component({
  selector: 'app-reviews',
  template: `
    <div id="googleReviews"></div>
    <section id='reviews'>
    <div id="title">
      <h4>Google Reviews</h4>
    </div>
    <div id="reviews" class="row">
      <mat-card *ngFor="let review of reviews" class="card">
        <mat-card-header>
          <div class="avatar" mat-card-avatar>
            <img src="{{ review.profile_photo_url }}" alt="Avatar" height="50" 
             width="50" />
          </div>
          <mat-card-title>{{ review.author_name }}</mat-card-title>
          <h6>{{ review.relative_time_description }}</h6>
          <div class="stars"><mat-icon *ngFor="let item of 
           createRange(review.rating)">grade</mat-icon></div>
        </mat-card-header>
        <mat-card-content>
          <p>{{ review.text }}</p>
        </mat-card-content>
      </mat-card>
    </div>
    <div class="rowButton">
      <a mat-raised-button href="${ALL_REVIEWS_LINK}" target="_blank">View all reviews</a>
    </div>
    </section>
  `,
  styleUrls: ['./reviews.component.scss']
})
export class ReviewsComponent implements AfterViewInit {
  service;
  public reviews = [];

  constructor() {}

  ngAfterViewInit() {
    const request = {
      placeId: GOOGLE_PLACE_ID,
      fields: ['reviews']
    };
    this.service = new google.maps.places.PlacesService(document.getElementById('googleReviews'));

    this.service.getDetails(request, this.callback);
  }

  public callback = (place, status) => {
    if (status === google.maps.places.PlacesServiceStatus.OK) {
      this.reviews = place.reviews.slice();
    }
  };

  createRange(number) {
    const items: number[] = [];
    for (let i = 1; i <= number; i++) {
      items.push(i);
    }
    return items;
  }
} 
```

在这个例子中，我把 html 模板放在。ts 文件，但如果你想你可以分开，并把模板在 reviews.component.html

你可以在这里找到你的 GOOGLE _ PLACE _ ID:[GOOGLE PLACE ID](https://developers.google.com/places/place-id)

ALL_REVIEWS_LINK 是一个链接，用于查看您网站的所有 google 评论。你可以在你的个人资料中找到这个链接。

* * *

这里有一些款式...

##### reviews.component.css

```
.row {
  align-items: center;
  flex-direction: column;
  align-content: center;
  display: flex;
  flex: 1 1 auto;
}

.rowButton {
  margin: 10px;
  display: flex;
  justify-content: center;

  a {
    background-color: #056571;
    color: white;
    font-family: 'Roboto', sans-serif;
    font-size: 1.5em;
    padding: 10px;
  }
  a:hover {
    box-shadow: 1px 1px 2px 2px rgba(0, 0, 0, 0.3);
  }
}

.card {
  background-color: whitesmoke;
  max-width: 250px;
  height: 300px;
  margin: 10px;
}

.mat-card {
  box-shadow: none;
}

.card:hover {
  box-shadow: 0 0 12px rgba(0, 0, 0, 0.3);
}

mat-card-content {
  height: 150px;
  text-align: justify;
  overflow-y: auto;
}

.avatar {
  margin-bottom: 15px;
}

mat-card-title {
  margin-bottom: 5px;
}

mat-card-header {
  display: flex;
  flex-direction: column;
  align-items: center;
}

mat-icon {
  color: #fad201;
  width: 10px;
  height: 10px;
  margin: 5px;
  padding: 5px;
  margin-bottom: 15px;
}

h6 {
  margin: 2px;
}

mat-card {
  display: block;
  position: relative;
  font-size: 20px;
  text-align: center;
  text-decoration: none;
  color: #262626;
  margin: 0 10px;
  transition: 0.5s;
}

mat-card span {
  position: absolute;
  transition: transform 0.5s;
}

#title {
  display: flex;
  flex-direction: row;
  justify-content: center;
  h4 {
    text-align: center;
    font-family: 'raisa', sans-serif;
    font-size: 90px;
    margin: 40px 0 40px 0;
    color: black;
    letter-spacing: 5px;
    font-weight: bold;
  }
} 
```

仅此而已。你现在可以看到你的客户的最后评论。