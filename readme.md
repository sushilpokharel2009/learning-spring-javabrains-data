## Starting off

We created this project in idea via spring initializr. We picked the Web, Data-JPA and Derby dependencies.

Derby is an embedded database that we are going to use for now. We'll switch to a legit one later.

We then copied over our course files from [the first project](https://github.com/KProskuryakov/learning-spring-javabrains).

Nothing else has been changed.

## Repositories

We want to link our existing Topic object to a table in our relational database. There is an annotation that we add to our Topic class for this to happen.

```java
@Entity
public class Topic { ... }
```

One of our private variables in the Topic has to be the primary key. `id` was intended as that so we add an annotation to that too.
```java
@Id
private String id;
```

Both of those should be importing from javax.persistence

Now we come to the repository. If we look at our TopicService, we defined some basic operations, namely:

* Get all Topics
* Create Topic
* Read Topic
* Update Topic
* Delete Topic

Most repositories will need these basic functions. So Spring does these for us. Later on we can define a few of our own if we need them for specific repositories.

Our repository is an interface which Spring is going to make a subclass of. Spring's existing implementation is called CrudRepository.

```java
public interface TopicRepository extends CrudRepository<Topic, String> { }
```

We extend our repository with the generic Spring one and give it the class that it'll use and the type of the ID that it'll use.

## CRUD methods

We're going to use our already existing TopicService to interact with the TopicRepository (since we need to fetch an instance of it, or rather spring will do that for us).

```java
// TopicService.java
@Autowired
private TopicRepository topicRepository;
```

Now we can access topicRepository's methods:

```java
topicRepository.findAll() // get all, returns an iterable
topicRepository.save(course); // adds/updates courses
topicRepository.findOne(id); // gets one course
topicRepository.delete(id); // deletes a course
```

`save()` can be used for both adding and updating.

We've updated our TopicService to now use the repository methods to manipulate the database data.

## Courses

Under our Topics we will have courses listed. So we just basically copied over and renamed the Topic classes and made them Course classes.

We need to change the routes since our courses will have topics.

```java
@RequestMapping("/topics/{topicId}/courses/{id}")
public Course getCourse(@PathVariable String id) { }
```

We also added a member variable to Course.java. We also added a ManyToOne annotation because many courses can have one topic.

```java
@ManyToOne
private Topic topic; // we also gave getters/setters and constructor
```

So in the repository we can add methods to do specific things like find all courses by the name. Spring JPA will make the method for us if we give it a good name.
```java
public Course findByName(String name);
```

To make it slightly more complicated, we add topicId to our CourseService findAllCourses decleration
```java
public List<Course> getAllCourses(String topicId) {}
```

This is our repository interface method that we added so that we can leverage the @ManyToOne annotation on the Topic member variable inside of Course.
```java
// CourseRepository
public List<Course> findByTopicId(String topicId);
```

At this point, unless I missed something, everything works with updating the database and stuff.