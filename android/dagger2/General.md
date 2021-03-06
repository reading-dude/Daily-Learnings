* [Very good codelab to understand dagger2](https://codelabs.developers.google.com/codelabs/android-dagger/#15)
## reflection 

* can access methods and variables of class at runtime 
* since its creates at runtime difficult to debug stacktrace based on configuration which happens at runtime crash
* Annotation processing is same as reflection but does it at build time was used in dagger1 for creating instance alone but still having 
  runtime crash  
  
## Inversion of control (IOC)
 
 * When classes need dependencies, create the instance and provide to required classes known as IOC
 * providing dependency from outside makes it configurable and easy to make change in only on dependencycomponent
*  every component should have a scope such as `@singleton` `@activityscope` searches for module's which provides the instances 
	anything that the application component holds should be singleton to access methods from application component you need to    create methods in the component, this allows the share the component for dependencies.
	
## Constructor Injection

* **Module** class will provide the methods through which the dependency will be provided to required classes
* **Component** will take modules that it will make use to build dagger component, Marked with singleton as we are providing networkservice and databaseservice or any other classes as a singleton only. Also application component will only take singleton scope
	* Component will have a method typically `inject()` which will take the class in which it will provide the dependency.
	* Will take modules array to build dependencycompoenent for different modules. 
* **Scope** - Every component will have a scope defined which will contain all modules to be used within that scope. 
* **Binds** - Use @Binds to tell Dagger which implementation it needs to use when providing an interface.

`@Binds` must annotate an abstract function (since it's abstract, it doesn't contain any code and the class needs to be abstract too). The return type of the abstract function is the interface we want to provide an implementation for (i.e. Storage). The implementation is specified by adding a unique parameter with the interface implementation type (i.e. SharedPreferencesStorage). 

```
@Module
abstract class StorageModule {

    // Makes Dagger provide SharedPreferencesStorage when a Storage type is requested
    @Binds
    abstract fun provideStorage(storage: SharedPreferencesStorage): Storage
}
```

* Use @BindsInstance for objects that are constructed outside of the graph (e.g. instances of Context).
	
## Providing dependency from another component

* we have to provide DatabaseService and NetworkService to mainviewmodel, since we have activity module in which we have provides()
 for mainviewmodel, if we provide a new instance of DatabaseService and NetworkService like below. 
 
 ``` 
 @Provides
    MainViewModel providesMainViewModel(DatabaseService databaseService,NetworkService networkService){
        return  new MainViewModel(databaseService, networkService);
    } 
 ```
* If we do this dagger will look for either the provides() for DatabaseService and NetworkService in above case its not present so
  it will throw error stating we need to provide one or use `@Inject`on constructor. 
* Since we don't want to create another instance of it and we can have a shared dependency from application component like 
  defining our `ActivityComponent` like below. 
  
  ```
   @Component(dependencies = {ApplicationComponent.class} ,modules = {ActivityModule.class})
   public interface ActivityComponent {

    void inject(MainActivity mainActivity);
  }
  ```
 * We get a error after building asking for `@Inject` or `@Provides` over databaseservice and networkservice, which means that we have 
   anything that we are trying to access from applicationcomponent we need to create a method for it. like below. 
   
   ```
     @Singleton
    @Component(modules = {ApplicationModule.class})
    public interface ApplicationComponent {

    void inject(MyApplication application);
    NetworkService getNetworkService();
    DatabaseService getDatabaseService();
    }
   ```
 * By doing this we will be able to access the methods from application component across other components like in our case activity
   component.
 * Now we have to set the application component that we have build in activity component otherwise the application would crash asking 
   us to set it like below. 
   
   ```
     public ApplicationComponent applicationComponent;

    @Override
    public void onCreate() {
        super.onCreate();

        applicationComponent = DaggerApplicationComponent.builder()
                .applicationModule(new ApplicationModule(this))
                .build();

               applicationComponent.inject(this);
   ```
   
 * We also have to include this the mainactivity while building activitycomponent as it will take the required dependency based on the 
   application component that it has build. 
	
 * When we use `@Inject` for any class dagger2 first checks for its constructor where we have provided `@inject` if yes then we don't have to use `@provides` annotation in the activity module like this 
   
   ```
   @Provides
    MainViewModel provideMainViewModel(DatabaseService databaseService,
                                       NetworkService networkService) {
        return new MainViewModel(databaseService, networkService);
    }
	```
## Injecting through constructor @Inject instead of @Provides 

* Replacing provides() of mainviewmodel with constructor @inject, as dagger will look for @inject first if not found then goes for 
  @provides
  
  ```
   @Inject 
    public MainViewModel(DatabaseService databaseService, NetworkService networkService) {
        this.databaseService = databaseService;
        this.networkService = networkService;
    } 
  ```
## Using qualifier to identify the datatypes instance of same type required for different methods

* We create two qualifier one for db and network and mention them at particular string to be identified

 * Qualifier
   * Suppose if we are providing instances of same  method and if it has same type in dagger to we use qualifier to separate them. 
    ```
	
	@DatabaseInfo
    @Provides
    String provideDbname(){
        return "abc";
    }
    @Provides
    Integer provideDbVersion(){
        return 1;
    }
    @NetworkInfo
    @Provides
    String provideApiKey(){
        return "xyz";
    }
	
	```
* Qualifier classes 

  ```
  
  @Qualifier
  @Retention(RetentionPolicy.SOURCE) // will be removed when compiling
  public @interface DatabaseInfo {
  }
   
  @Qualifier
  @Retention(RetentionPolicy.SOURCE) // will be removed when compiling
  public @interface NetworkInfo {
  }

  ```
  
* Using qualifier in DatabaseService and NetworkService

```
   
    @Inject
    public DatabaseService(Context context, @DatabaseInfo String databaseName, int version) {
        // do the initialisation here
        this.context = context;
        this.databaseName = databaseName;
        this.version = version;
    }
    
    @Inject
    public NetworkService(Context context, @NetworkInfo String apiKey) {
        // do the initialisation here
        this.context = context;
        this.apiKey = apiKey;
    }

```
   * Above we have two String types which are passed to db class as constructor arguments, if you try to build it it would throw error saying that 
   "String is bound multiple times", we add qualifiers to avoid this issue. 
   * We can create qualifiers with @Qualifier such as 
   
   ```
    Qualifier
    @Retention(RetentionPolicy.SOURCE)
      public @interface DatabaseInfo {
       }
	   
	   and 
	   
	   @Qualifier
   @Retention(RetentionPolicy.SOURCE)
   public @interface NetworkInfo {
   }
	   
   ```
   
   * Now using this I can mention in the db class as below. 
   
   ```
   private Context context;
    private String databaseName;
    private int version;

    @Inject
    public DatabaseService(@ApplicationContext Context context,
                           @DatabaseInfo String databaseName,
                           @DatabaseInfo Integer version) {
        // do the initialisation here
        this.context = context;
        this.databaseName = databaseName;
        this.version = version;
    }
   
   ```
   
 ## Subcomponent 
 
 [Stackoverflow post](https://stackoverflow.com/questions/29587130/dagger-2-subcomponents-vs-component-dependencies)
 
 ## Blogs
 
 [https://proandroiddev.com/dagger-2-part-ii-custom-scopes-component-dependencies-subcomponents-697c1fa1cfc]
 [http://frogermcs.github.io/dependency-injection-with-dagger-2-custom-scopes/]
 * [Dagger optimizations in kotlin](https://medium.com/androiddevelopers/dagger-in-kotlin-gotchas-and-optimizations-7446d8dfd7dc?source=---------7----------------)
