## Dependency Injection
In object-oriented programming (OOP) software design, dependency injection (DI) is the process of supplying a resource that a given piece of code requires. The required resource, which is often a component of the application itself, is called a dependency.

Consider the following Java class.

    public  class  User{    
	    private  Database mDatabase;    
	    public  User(){	    
		    mDatabase  =  new  Database();	    
	    }    
    }
In the above class, we are creating the object of class **Database** inside the constructor of class **User**. So here, we can say that; the User is dependent on Database. So here, **Database** is a dependency for the class **User**.

It is indeed a bad idea. We should keep the classes as independent from other classes. It has the following advantages:
 - If the classes are independent, you can reuse them.
 - For unit test each class, it is necessary that the classes are
   independent of each other.

Instead of using new to instantiate the Database inside User’s constructor we can pas the Database as an argument to the User’s constructor.

    public class User{
	    private Database mDatabase;
	    public User(Database mDatabase){
		    this.mDatabase = mDatabase;
	    }
    }
So this is what we call as **Dependency Injection**. Means supplying the dependencies from outside the class. And more specifically for the above code, we can consider it as a *Constructor Injection*.

Dependency Injection in build upon the concept of Inversion of Control. That means a class should get its dependencies from outside. 
In more simpler words, your class cannot instantiate another class using a new keyword inside it. Instead, you have to supply the object from outside.

**What is Dagger 2?**
Dagger is a fully static, compile-time dependency injection framework for both Java and Android. It is an adaptation of an earlier version created by Square and now maintained by Google. 

**Dependency Provider**
Dependencies are the objects that we need to instantiate inside a class. And we learned before that we cannot instantiate a class inside a class. Instead, we need to supply it from outside. So the person who will provide us the dependencies (objects) is called *Dependency Provider*.
in **dagger2** the class that you want to make a Dependency Provider, you need to annotate it with the **@Module** annotation. And inside the class, you will create methods that will provide the objects (or dependencies). With dagger2 we need to annotate these methods with the **@Provides** annotation.

**Dependency Consumer**
It is a class where we need the objects. While using dagger we don't even need to get it as an argument. Dagger will provide the dependency, and for this, we just need to annotate the object declaration with **@Inject.**

**Component**
This is an interface that makes some connection between dependency provider and dependency consumer. For this we need to annotate it with **@Component** and rest of the thing will be done by Dagger.

Create a class called Database.java. We need to use the data inside the database. It is called the **dependency**. This is just a model class. No annotation is required.

    public class Database {  
      private String name;        
      public Database(String name) {  
            this.name = name;  
      }        
      public String getName() {  
            return name;  
      }  
    }
Now create a provider class called AppModule

    import javax.inject.Singleton;  
    import dagger.Module;  
    import dagger.Provides;    
    
    @Module  
    class AppModule {  
      private Database database;    
      AppModule(Database database) {  
          this.database = database;  
      }    
      @Provides  
      @Singleton  public Database getDatabase() {  
	      return database;  
      }  
    }
Now we create an interface called AppComponent

    import javax.inject.Singleton;    
    import dagger.Component;  
      
    @Singleton  
    @Component(modules = {AppModule.class})  
    public interface AppComponent {  
        void inject(MainActivity activity);  
    }
In application class we are building the injection as follows

    import android.app.Application;  
      
    public class MyApplication extends Application {    
    private AppComponent mAppComponent;  
      
      @Override  
      public void onCreate() {  
	      super.onCreate();  
	      Database database = new Database("My DB");  
	      mAppComponent = DaggerAppComponent.builder()  
                    .appModule(new AppModule(database))  
                    .build();  
      }  
      
      public AppComponent getNetComponent() {  
	      return mAppComponent;  
      }  
    }
and we can inject the dependency as follows

    import android.support.v7.app.AppCompatActivity;  
    import android.os.Bundle;  
    import android.util.Log;        
    import javax.inject.Inject;  
      
    public class MainActivity extends AppCompatActivity {        
      @Inject Database database;  
      @Override  
      protected void onCreate(Bundle savedInstanceState) {  
	      super.onCreate(savedInstanceState);  
	      setContentView(R.layout.activity_main);  
      
	      ((MyApplication) getApplication()).getNetComponent().inject(this);  
	      Log.e("database",database.getName());  
      }  
    }






Reference : https://www.simplifiedcoding.net/android-dependency-injection/
