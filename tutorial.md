## Basic Nuxt + Storyblok setup

Signup for a free Storyblok account & choose to start with an empty space. You will be guided to a screen that looks like below.

![STORYBLOK START SCREEN]()

We will now click on that `Home` content entry and download a preconfigured Nuxt.js project based on this [Github Repository](https://github.com/storyblok/vue-nuxt-boilerplate). You can also have a look at [our full website nuxt tutorial](https://www.storyblok.com/tp/nuxt-js-multilanguage-website-tutorial) linked next to the download button. The project you will be downloading already is configured with your `previewToken` that can be found in your space dashboard if you need it. 

### Install dependencies

The project you've downloaded is a basic Nuxt Startertemplate with the default components in the home entry. So to get everything running we need to install the project dependencies.

```
npm install
# or
yarn
```

### Start project

After installing the dependencies you can start your nuxt development with the command below. You will be able to access your app via `http://localhost:3000`

```
npm run dev
```

The last step to have the live-preview and editing running is to tell the Storyblok interface that you're running your server on `localhost:3000`. Enter `localhost:3000` in the second step as shown below and press `Go` afterward.

![second step]()

After pressing `Go` you should see a quick reload and switch of the onboarding screen to your own project embedded in Storyblok. You can now press into those components and edit the content on the right side. Try pressing on the `Feature 2` and enter another content in the `name` field to see it change, make sure to press `Save` to actually save your changes.

### Define & Create Product Details

Since we not only want a startpage we will now have to setup the content structure to allow us to create products. To do so we press the Storyblok Logo at the top right to get back to content overview in Storyblok. Once back at the overview we create a new folder with the button on the top right with the name `Products`.

![Products creation dialog]()

Before pressing `Save` we will also directly add a new content type. To increase your effeciency you will not have to go back to `Components` and first create the product schema - you can do that directly during content creation as well. So we will go ahead and enter `product` in `Default Content Type`, even tho it does not exist yet. Press the `Create new "product"` button below the input and continue with finally pressing `Save`.

![Products creation dialog with content type]()

The final step is the create our first product and define the fields we want to have for our products. You should already be inside the Products folder so you can now already press the `+ Entry` button. You should see that the content type should already be set to "product" and all that's left for you is to choose the name of your first product. I'll go ahead and choose `Alphachoice Fitness Towel` because [Alphachoice](https://www.storyblok.com/cs/alphachoice) uses the Storyblok + Snipcart combination to boost their ecommerce business! 

![Screen after content entry creation]()

You will notice that you won't see anything in the live-preview, that's because we don't have a `product` component yet defined in the nuxt setup that would allow us to display something and we also do not have any fields defined. To do so we navigate into the `Compose` Mode and then press the `Define Schema`. If you don't want that live-preview you can use the `form only` option in the left sidebar, which is accessible via the Edit menu.

![How to define Schema]()

Next thing to do to define the fields we need for our product. Let's start with fields like `name`, `price`, `description` and `images`. Enter one key at a time and press `+ Add` - make sure to choose the field types you think are appropriate for those fields (eg. Text for `name`, Multi for `images`, Number for `price`, ...).

![Define your schema]()

Once finished, you can now press `Save Schema` at the top right corner and you're ready to insert the data you want.

![Insert your information and price, upload some images]()

After entering your information press the `Save` button to make your information accessible via API and stored in Storyblok, you can also press Publish to make it accessible not only in the draft version but also with the `version=published` param one would use for a production environment.

## Use the Product Detail information in Nuxt

The setup you've downloaded is configured to dynamically include components and already load your data from Storyblok according to the slug we've entered. You may realize that since we created a folder `Products` it also gave us a slug `products` and since our product is called `Alphachoice Fitness Towel` we already got `alphachoice-fitness-towel` as the second part, leaving us with the full slug `products/alphachoice-fitness-towel`.

### Create the Product.vue

Our Content type is called `product` therefore we create a component with the exact same: `Product.vue` inside the `components` folder. Below you can find the content of the `Product.vue`. You can access the fields you defined in the schema by using `blok.YOUR_KEY` to display the information. I've also added a quick & dirty image gallery to make it look a bit fancy.

~~~javascript
<template>
  <div class="product" v-editable="blok">
    <div class="product__images">
      <div v-if="blok.images.length > 0">
        <img :src="selectedImage.filename || blok.images[0].filename" :alt="selectedImage.name || blok.images[0].name">
      </div>
      <ul class="product__thumbs">
        <li class="product__thumb" v-for="image in blok.images" :key="image.filename" @click="selectedImage = image">
          <img class="product__thumb-image" :src="image.filename" :alt="image.name">
        </li>
      </ul>
    </div>
    <div class="product__information">
      <h1 class="product__headline">{{ blok.name }}</h1>
      <div class="product__price">€ {{ blok.price }}</div>
      <div class="product__description">{{ blok.description }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['blok'],
  data() {
    return {
      selectedImage: {}
    }
  }
}
</script>

<style>
.product {
  padding-top: 60px;
  margin: 0 auto;
  max-width: 1000px;
  display: flex;
  flex-direction: row;
}

.product__thumbs {
  margin-top: 50px;
}

.product__thumb {
  display: inline-block;
  padding-right: 10px;
  list-style: none;
  width: 55px;
  margin-right: 10px;
  padding: 5px;
  border: 1px solid #ccc;
}

.product__thumb-image {
  max-width: 100%;
}

.product__images {
  margin: 0 0.5rem;
  width: calc(100% / 3 * 2);
}

.product__information {
  margin: 0 0.5rem;
  width: calc(100% / 3 * 1);
}

.product__price {
  font-size: 30px;
  margin-top: 20px;
  margin-bottom: 25px; 
}

.product__description {
  margin-bottom: 20px;
}

.product__description h2 {
  margin-top: 20px;
  margin-bottom: 10px;
}

.snipcart-add-item {
  appearance: none;;
  display: inline-block;
  padding: 15px 20px;
  cursor: pointer;
  font-size: 20px;
  background: #000;
  color: #fff;
}

.alphachoice {
  color: #000;
  font-size: 80%;
  display: inline-block;
  padding-top: 5px;
}
</style>
~~~

Make sure to register your `Product.vue` in the `plugins/components.js`: 

~~~
import Product from '~/components/Product.vue'

...

Vue.component('blok-product', Product)
~~~


![Product vue result version one]()


## Using Markdown to render the description

Markdown is an amazingly fast way to bring formatted content to your website without the need to write HTML. We choose `markdown` for our `description` field to have a WYSIWYG editor field in the sidebar, to render that content we can use the library `marked`.

~~~
npm install marked --save
~~~

And add a new filter to `/plugins/filters.js` to have it accessible in our Vue Templates. You may see another `resize` filter available that uses our Image Service so we can also utilize it to resize our images to increase our page speed.

~~~javascript
import marked from `marked`

...

Vue.filter('markdown', (markdown) => {
  if (typeof markdown !== 'undefined') {
    return marked(markdown)
  }
  return null
})
~~~

Now let's update our template in `Product.vue` to use markdown and the resize filter.

~~~html
<template>
  <div class="product" v-editable="blok">
    <div class="product__images">
      <div v-if="blok.images.length > 0">
        <img :src="(selectedImage.filename || blok.images[0].filename) | resize('550x0')" :alt="selectedImage.name || blok.images[0].name">
      </div>
      <ul class="product__thumbs">
        <li class="product__thumb" v-for="image in blok.images" :key="image.filename" @click="selectedImage = image">
          <img class="product__thumb-image" :src="image.filename | resize('55x55')" :alt="image.name">
        </li>
      </ul>
    </div>
    <div class="product__information">
      <h1 class="product__headline">{{ blok.name }}</h1>
      <div class="product__price">€ {{ blok.price }}</div>
      <div class="product__description" v-html="$options.filters.markdown(blok.description)"></div>
    </div>
  </div>
</template>
~~~

Resulting in smaller images for the thumb and a pretty description.

![result after adding markdown]()

## Adding a Snipcart Buy Button

To add a Snipcart buy button we will have to signup for an account, check out [their Pricing page](https://snipcart.com/pricing) to get the best deal for you, go to [https://app.snipcart.com/account/register](https://app.snipcart.com/account/register) to register.

![Image of Snipcart]()

Once you’ve signed up and confirmed your account, log in and head to the `Account -> Credentials` section. This is the place where you’ll find your API key. You will be in the test mode by default so you will need your test API key to go on. Once you have your API key, we will need to include the [Snipcart](https://snipcart.com/) javascript and CSS files. With Nuxt, we will have to modify to `nuxt.config.js` to add the required styles and scripts as shown below.

~~~javascript
...

/*
** Headers of the page
*/
head: {
  ...
  link: [
    { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' },
    { rel: 'stylesheet', href:'https://cdn.snipcart.com/themes/2.0/base/snipcart.min.css' }
  ],
  script: [
    { src: 'https://ajax.googleapis.com/ajax/libs/jquery/2.2.2/jquery.min.js' },
    { src: 'https://cdn.snipcart.com/scripts/2.0/snipcart.js', id: 'snipcart', 'data-api-key': 'YOUR_API_KEY_GOES_HERE' }
  ]
},

...
~~~

Since we now do have the script embedded we're finally ready to insert our buy button, to do so - let's open the `Product.vue` again and add it below the `description`.

~~~html
<template>
  <div class="product" v-editable="blok">
    <div class="product__images">
      <div v-if="blok.images.length > 0">
        <img :src="(selectedImage.filename || blok.images[0].filename) | resize('550x0')" :alt="selectedImage.name || blok.images[0].name">
      </div>
      <ul class="product__thumbs">
        <li class="product__thumb" v-for="image in blok.images" :key="image.filename" @click="selectedImage = image">
          <img class="product__thumb-image" :src="image.filename | resize('55x55')" :alt="image.name">
        </li>
      </ul>
    </div>
    <div class="product__information">
      <h1 class="product__headline">{{ blok.name }}</h1>
      <div class="product__price">€ {{ blok.price }}</div>
      <div class="product__description" v-html="$options.filters.markdown(blok.description)"></div>

      <button
        class="snipcart-add-item"
        :data-item-id="blok._uid"
        :data-item-name="blok.name"
        :data-item-price="blok.price"
        :data-item-url="$nuxt.$route.path">
            Buy {{blok.name}}
      </button>
      <a class="alphachoice" href="https://www.alphachoice.de/products/alphachoice-fitness-towel" target="_blank">Shop the real thing</a>
      
    </div>
  </div>
</template>
~~~

You can see that since every attribute we need is already available in the `blok` variable, we can use it for the Snipcart button as well. 

![Result with Snipcart Button]()

> “We at Alphachoice started with a simple HTML page and integrated the e-commerce solution Snipcart to sell our items. Starting with the first product sale we always kept the focus on not only selling a product - but value - since behind every item we have a Story to tell. The combination of Storyblok as CMS and Snipcart as our commerce solves most of our challenges - we can add new products in Storyblok in different sizes, colors, and materials in a breeze.” David Schörkmayr, Chief Executive Officer, Alphachoice GmbH

## Final Step: Product Overview

Last step we want to create a product overview. To do so we will create the file `pages/products.vue` in the `pages` folder with the content below. In this page, we will load all content entries from Storyblok that `starts_with` `products` since those are the entries in our products folder. 

```javascript
<template>
  <div class="products">
    <h1>Products</h1>
    <ul class="overview">
      <li class="overview__item" v-for="product in stories" :key="product.full_slug">
        <nuxt-link class="overview__item-inner" :to="'/' + product.full_slug">
          <h2>{{product.content.name}}</h2>
          <img width="100%" v-if="product.content.images.length > 0" :src="product.content.images[0].filename | resize('300x0')" :alt="product.content.images[0].name">
        </nuxt-link>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data () {
    return { stories: [] }
  },
  asyncData (context) {
    // Check if we are in the editing mode
    let editMode = false

    if (context.query._storyblok || context.isDev || (typeof window !== 'undefined' && window.localStorage.getItem('_storyblok_draft_mode'))) {
      if (typeof window !== 'undefined') {
        window.localStorage.setItem('_storyblok_draft_mode', '1')
        if (window.location == window.parent.location) {
          window.localStorage.removeItem('_storyblok_draft_mode')
        }
      }
      editMode = true
    }
    return context.app.$storyapi.get(`cdn/stories/`, {
      starts_with: 'products/',
      version: editMode ? 'draft' : 'published',
      cv: context.store.state.cacheVersio
    }).then((res) => {
      return res.data
    }).catch((res) => {
      if (!res.response) {
        console.error(res)
        errorCallback({ statusCode: 404, message: 'Failed to receive content from the api.' })
      } else {
        console.error(res.response.data)
        errorCallback({ statusCode: res.response.status, message: res.response.data })
      }
    })
  }
}
</script>

<style>
.products {
  padding-top: 60px;
  margin: 0 auto;
  max-width: 1000px;
}

.overview {
  display: flex;
  flex-direction: row;
  margin: 0px;
  padding: 0px;
  margin-top: 20px;
}

.overview__item {
  margin: 0 0.5rem;
  width: calc(100% / 3 * 1);
  list-style: none;
}

.overview__item-inner {
  padding: 24px;
  margin-bottom: 10px;
  box-shadow: 0 2px 4px 0 rgba(14,30,37,.36);
  text-decoration: none;
  color: #000;
  display: block;
}
</style>
```

You can add more products to have them show up on the overview.

![Product overview result]

## Summary

With Storyblok you will get a flexible CMS that allows you to combine it with Nuxt.js and Snipcart in no time. The combination with Storyblok and Snipcart is as easy as it can be because Snipcart makes it unbelievably easy to be configured using HTML attributes and their JS, and Storyblok makes it easy for you to define the fields you want to display. You can now have a look at our full-blown website Nuxt.js tutorial to learn more, build a multi-language setup and add a header to your shop. Make sure to [check out Alphachoice](https://www.alphachoice.de/) to see Storyblok + Snipcart in action. 
