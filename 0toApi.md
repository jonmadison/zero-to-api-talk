build-lists: true
footer: 0 to API in node.js, @jonmadison, Seattle Code Camp 2015
slidenumbers: true

# [fit] 0 to API in node.js

^before the talk:

^sudo su - jon -c  'postgres -D /usr/local/var/postgres'

^cd ~/presentations/0toAPI/ft

^subl .

---
![right](./images/yes2.gif)

#Have you ever wanted to 

- start a web app from scratch?
- write a mobile service?
- write software for a microcontroller?
- make data available in the cloud?

^0:00

^Okay -- this is the only time i'll be extremely pitch-y, so bear with me...:)

^[SLIDE] are you thinking of starting a web site/app from scratch? 
^[SLIDE] writing mobile service? 
^[SLIDE] Writing software for an embedded device or microcontroller? [SLIDE] or data in the cloud?
^[SLIDE] Have you considered exposing functionality as an API, first? You should.

^Over the past few years i've had a lot of fun working on over a dozen projects to support experiments, in store experiences, hackathons, all using an API-first approach, and in particular, node.js. 

^I want to convince you to start any of your network enabled projects by thinking of your API first, consider node.js, and giving you some pointers in the right direction.

---
![filtered](./images/key.jpg)
#key takeaways

- When solving problems, build an API first. You'll usually thank yourself in the end.
- design your [APIs] by writing tests. 
- secure your API asap
- node.js is a bit of fun when it comes to doing this stuff.

^0:02

---
#Why API, first?
![fill](./images/why.gif)

^0:03

---
#Why API, first?
![fill](./images/why.png)
![right 95%](./images/service_orient.jpg)

- decoupling views from logic
- helps you think more clearly of testing first
- setting you up for the future (µServices-ready)
- there are tradeoffs

^0:03

^Why focus on APIs (or, Interfaces) first? I mean, this is nothing new, computer science has been preaching about talking in terms of interfaces for like, 5ever
     * how many front end devs here?
     * how many back end?
     * full stack?
     * how many of you build web apps, or will build one soon?
     * how many of you have worked with APIs?
        * written from scratch?


^In the age of SPAs, we tend to think this way anyway, but why?


^[SLIDE] ...you'll be exposing your info in a way that many different types clients can get at, including ones you hadn’t thought of.


^[SLIDE] i find it helps me think of testing in a better way. i’m immediately thinking in terms of “given this i want that”, which tends to translate nicely into a test i need to write.


^[SLIDE] who knows how someone will use your services. build to inspire.

^[SLIDE] as with anything there are tradeoffs -- different sets of security concerns now that we've decoupled, performance considerations, but i'm here to convince you that it's worth it in the end ;-)

---
#Why node.js?
![fill filtered](./images/quirky.png)

- minimal
- powerful
- additive, not subtractive (most of the time)
- it's javascript, with all its quirkiness

^0:07

^When i first started with web development, a short eon ago, i was doing sites in CGI using C and Perl. 

^Then PHP came along...i could build an entire site within a pages! (which actually turns out not to be a great thing)

^Then J2EE came along. It's there where i was introduced to MVC. 

^ About 3 and a half years ago when i joined the innovation lab at Nordstrom, I was exposed toRuby on Rails...it was *the* way. I'd built web services before, but hadn't done "proper REST". I cut my teeth with using proper HTTP verbs all the time (i hadn't realized people were actually doing that properly, lol. I mean, most of my API interaction had been with the Flickr API...

^It's a decent framework. That said, Rails is heavy. It gives you a *lot*, and it was always tough to understand what i could do with and without. 

^I was introduced to node.js a year or so later, and it resonated withh the old school Unix hacker in me. [SLIDE] It was minimal enough for me to understand its pieces, but [SLIDE] powerful when the pieces were put together. The most widely used framework for Node, Express, if i had to compare in Ruby terms, is more like Sinatra than Rails.

^[SLIDE] again, with the Unix philosophy--node allows you to add modules as you need them, and the philosophy is generally to make them pretty small. Substack -- 782 repos last count. that's one (well, very productive) developer!

^[SLIDE] I happen to enjoy javascript. It's deceptively simple, and is very zelig-like. You can get into real trouble because of it's bad design pieces, but the support aro

---
![fill filtered](./images/baby1.jpg)
#Let's start with an idea

You have an idea, or job to be done

- data you want available to a mobile or web site or app?
- gather data from multiple clients (signing up customers? weather sensor?)
- mashing up multiple APIs

^0:10
Let's get into it. Why would i want to spin up an API just like that?

---
![fill](./images/flower0.jpg)
#Today's idea - flowers!
![inline](./images/usda.png)

^0:11
I had a concept a few years back called "flower thing", which was supposed to be a site to help people envision, create, and share flower garden ideas and the like. It had a component where you could research viable flowers in your locale. To feed this, i took data from a government db of flowers http://plants.usda.gov/java/. it's pretty much a PITA to get the data from this database http://plants.usda.gov/adv_search.html. I re-exposed it to my rails web app. What i’d like to do is take this data instead and expose it as a pure API, so i can write both a web and mobile app.

---
#Before we start ``jjvf

- express (from StrongLoop)
- mocha
- js-must (assertion library)*
- supertest
- data store 

^0:15

^[SLIDE] How many here have used express? I won't get into details, just enough to work through the examples. The website explains it well: it's a "Fast, unopinionated, minimalist web framework for Node.js". it's middleware-based. its "app.use" construct is kinda like a Pipeline pattern. 

^[SLIDE] mocha is a test framework for javascript. There are others, and I encourage you to find one that suits your needs. Mocha suits mine.

^js-must: There are several assertion libraries--i think should is one of the most popular, but i recently came across must and i'm sold, as it seems to fix a few gotchas with the should library. Chai is also a popular expectations library, that claims to get you closer to BDD.

---
#Create the project

```bash
% sudo npm install -g nodemon
% sudo npm install -g express-generator
% express ft --git
% cd ft 
[get rid of cruft]
```

^Now that we have that out of the way, let's get some stuff installed that'll help us along

^Thing is, express-generator is okay, but it assumes that you're building an entire website. So even the minimal set up it gives is a little too much, so there's cruft to remove in the directory structures, and the app driver. I detail that in the github repo notes.

---
![fill filtered](./images/rest1.jpg)
#About REST
- Google "Roy Fielding Chapter 5"
- an *architectural style* that defines constraints around how resources on a web server are accessed
- Resources are nouns
- URLs should be discoverable
- be consistent

^How many of you know REST
^ Roy fielding first proposed it, ch. 5 is the canon
^ you want the URLs to be discoverable (guessable without a lot of mind-bending)
^ operations should be idempotent
^ spend time looking at other APIs, talking to other devs & archs...reading up: Apigee has a great guide
^ you can use headers to provide additional functionality. 
^ consistency is key
^ are there different ways? "better" ways? yeah. but...

---
![fill filtered](./images/rest1.jpg)
#Endpoints
- looking at plants
     - GET /plants
     - GET /plants/:id
     - GET /plants?name=<term>
     - *POST /plants*
     - *PATCH /plants/:id*
     - [...]

^The Resources are the *nouns*, HTTP verbs are the...

^Again, it's an architectual style, not dogma or guideline. There are some common sense decisions that should be able to be made knowing that: 


---
#Hello world of tests, i guess

```javascript
describe('basics',function(){
     it('should return 404 on /',function(done){
          request(app)
          .get('/')
          .set('Accept','application/json')
          .expect(404,done);
     })
});
```

^0:25

---
#Time for a real test - one assertion style

```javascript
     it('should return a list of plants',function(done){
          request(app)
          .get('/v1/plants')
          .set('Accept', 'application/json')
          .expect('Content-Type',/json/)
          .expect(200, function(err,res) {
\              demand(err).be.empty();
               demand(res.body).not.be.empty();
               demand(res.body.plants.length).equal(100);
               //get an example record
               demand(res.body.plants[0]).not.be.empty();
               demand(res.body.plants[0].name).not.be.empty();
               demand(res.body.plants[0].scientific_name).not.be.empty();
               done();
          })
     });
```

^0:25

^[SUBLIME TEXT] 1-test tab

^There are a few ways we could do the assertion. The "demand" style seems to lend itself more to BDD.

^[SUBLIME TEXT] rouotes/plants.js, 1-route tab

^[SUBLIME TEXT] controllers/plants.js, 1-impl tab

^now let's run this test.

^mocha test

---
#Expect a specific result

```javascript
     it('should return a particular plant',function(done){
          request(app)
          .get('/v1/plants/17590')
          .set('Accept', 'application/json')
          .expect('Content-Type',/json/)
          .expect(200, {   
                         id: 17590,
                         name: 'Agardh lupine',
                         description: null,
                         create_dt: null,
                         user_id: null,
                         zones: null,
                         created_at: 'Mon Sep 07 2015 06:50:06 GMT-0700 (PDT)',
                         updated_at: 'Mon Sep 07 2015 06:50:06 GMT-0700 (PDT)',
                         color: '',
                         color_family: null,
                         states: 'USA (CA)',
                         growth_season: '',
                         shopping_list_id: null,
                         garden_id: null,
                         duration: 'Annual',
                         scientific_name: 'Lupinus agardhianus',
                         foliage_color: '',
                         seed_color: null,
                         fruit_color: '',
                         shade_tolerance: null },done);
     });

```

^0:25

^[SUBLIME TEXT] 2-test tab

^Say you already had your desired data stubbed out (you used json-generator or some such), you can directly use them in your expectations like so

^[SUBLIME TEXT] routes/plants.js 2-route tab

^[SUBLIME TEXT] controllers/plants.js 2-impl tab

---
#Support querying

```javascript
     it('should return a particular plant via query',function(done){
          request(app)
          .get('/v1/plants/?name=Agardh%20lupine')
          .set('Accept', 'application/json')
          .expect('Content-Type',/json/)
          .expect(200, {    id: 17590,
                           name: 'Agardh lupine',
                           description: null,
                           create_dt: null,
                           user_id: null,
                           zones: null,
                           created_at: '2015-09-07T13:50:06.654Z',
                           updated_at: '2015-09-07T13:50:06.654Z',
                           color: '',
                           color_family: null,
                           states: 'USA (CA)',
                           growth_season: '',
                           shopping_list_id: null,
                           garden_id: null,
                           duration: 'Annual',
                           scientific_name: 'Lupinus agardhianus',
                           foliage_color: '',
                           seed_color: null,
                           fruit_color: '',
                           shade_tolerance: null },done);
     });

```

^0:30

^[SUBLIME TEXT] 3-test tab

^Say you already had your desired data stubbed out (you used json-generator or some such), you can directly use them in your expectations like so

^[SUBLIME TEXT] controllers/plants.js 2-refactor tab

^We'll stop here. In a fleshed out API, we'd handle pagination, have more search options, etc. as needed. Let's get this pushed to the Nets!

---
![right](./images/internet_surf.gif)
#deploy to the Internet!

```bash
% heroku login
% heroku create ftapi-ex
% heroku addons:create heroku-postgresql
% vi .gitignore
% git add .
% git commit -am"net push"
% git push heroku master
```

^0:35

^heroku create -- will create your app. Take a look -- you're live! well, your site is at least. We need to do a little more configuration to get up and running.

^we'll need to add the postgres Haddon.

^edit your .gitignore to include node_modules/

^commit, and push. it really is like magic...heroku recognizes that you're building a node app--our app already is looking for the DATABASE_URL variable 

---
#[fit]example client: iOS


^0:40

^I built an example iOS app that will let you see the list of plants, and get details on a single one. It took less than an hour to throw together. And yes, it looks like it. sorrry!



---
#Security considerations
![right 75%](./images/cors.jpg)

- CORS + jsonp

^0:45

^ [SLIDE] Browsers generally have a "Same Origin Policy" -- you can't share your API with web apps that are hosted outside of yours. 

^ 2 ways to do this: CORS, and jsonp. CORS is less hacky, but also doesn't have 100% browser support. JSONP is a standard hack that 


---
#Security considerations
![inline](./images/cors_browser_support.png)

---
#Security considerations
![right 75%](./images/passport.png)

- CORS + jsonp
- Secure your endpoints using PassportJS 
     - passport-local + passport-http-bearer
     - jwt-simple
     - Sign your Tokens
- use HTTPS on the Internet, always.

^0:45

^ [SLIDE] Browsers generally have a "Same Origin Policy" -- you can't share your API with web apps that are hosted outside of yours. 

^ 2 ways to do this: CORS, and jsonp. CORS is less hacky, but also doesn't have 100% browser support. JSONP is a standard hack that 

^ [SLIDE] PassportJS is an great middleware for providing authentication, and comes with dozens of strategies (ways to authenticate). 

^ I manage a secret key in my apps that i use to sign my tokens. Using heroku it's easy to rotate this key (though you'd have to invalidate sessions)

^ [SLIDE] Unfortunately I don't have time to walk through the configuration of PassportJS in your app; There's enough info for it's own talk.

^ There are a whole host of security considerations you should be familiar with, depending on the fidelity of what you're building.

---
#Summary

- When solving problems, build an API first. You'll usually thank yourself in the end.
- design your [APIs] by writing tests. 
- secure your API as soon as possible.
- i hope you've seen that node.js is a bit of fun when it comes to doing this stuff.

^0:55

---
![fill filtered](./images/library.jpg)
#Resources
[The World's Most Misunderstood Programming Language](http://www.crockford.com/javascript/javascript.html)

[JavaScript: the Weird Parts](https://www.udemy.com/understand-javascript/)

[ExpressJS crash course from StrongLoop](https://www.youtube.com/watch?v=aHqnFWLP7wA)

[Token Authentication and Single Page Apps](https://stormpath.com/blog/token-auth-spa/)

[Javascript: Understanding the weird parts](https://www.udemy.com/understand-javascript/)

^I can't stress this enough--if you're working in JavaScript, take some time to really get to know it. the "Understanding the weird parts" talk is one of the best resources i've found thus far on the subject. One of the best $32 you'll spend as a developer.

---
#Questions?
#@jonmadison
#jon madison on most of the things
![inline](./images/blackhandside.gif)
