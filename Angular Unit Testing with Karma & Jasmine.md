# What?
Unit Testing is a type of software testing where individual units or components of a software are tested. The purpose is to validate that each unit of the software code performs as expected.

- Unit Testing is done during the development (coding phase) of an application.
- Done by the developers.
- Unit Tests isolate a section of code and verify its correctness.
- A unit may be an individual function, method, procedure, module, or object.

> Unit Testing ➡ Integration Testing ➡ System Testing ➡ Acceptance Testing

# Why?
- Unit tests help to fix bugs early in the development cycle and save costs.
- It helps the developers to understand the testing code base and enables them to make changes quickly.
- Good unit tests serve as project documentation
- Unit tests help with code reuse. Migrate both your code and your tests to your new project. Tweak the code until the tests run again.

### Advantages
- The earlier a problem is identified, the fewer compound errors occur.
- Fixing problems early is usually cheaper than fixing them later in development.
- Easier debugging processes.
- Developers can quickly make changes to the codebase.
- Developers can reuse code and migrate it to new projects.

### Disadvantages
- Tests will not uncover every bug.
- Unit tests only test sets of data and its functionality -- it will not catch errors in integration.
- More lines of test code may need to be written to test one line of code -- creating a potential time investment.
- Developers may have to learn new skills to implement unit testing correctly; for example, having to learn how to use specific automated software tools.

# Unit Testing in Angular
### Test Runner: Karma
Karma is a testing tool developed by the AngularJS team, so it’s understandable why it’s usually associated with the framework. However, there’s nothing preventing you from using Karma with regular JavaScript apps.

### Test Framework: Jasmine
Jasmine is a behavior-driven development framework for testing JavaScript code. It does not depend on any other JavaScript frameworks. It does not require a DOM. And it has a clean, obvious syntax so that you can easily write tests.

## Configuration
```ng new``` The Angular CLI downloads and installs everything you need to test an Angular application with Jasmine testing framework out of the box including the .spec files.
```ng test``` will show test results.

### karma.conf.js
Sets up a test server to run tests automatically whenever a file changes.
```js
module.exports = function (config) {
  config.set({
    basePath: '',
    frameworks: ['jasmine', '@angular-devkit/build-angular'],
    plugins: [
      require('karma-jasmine'),
      require('karma-chrome-launcher'),
      require('karma-jasmine-html-reporter'),
      require('karma-coverage-istanbul-reporter'),
      require('@angular-devkit/build-angular/plugins/karma'),
	  require('karma-junit-reporter')
    ],
    client: {
      clearContext: false // leave Jasmine Spec Runner output visible in browser
    },
    coverageIstanbulReporter: {
      dir: require('path').join(__dirname, './coverage/claims-payment'),
      reports: ['html', 'lcovonly', 'clover', 'text-summary'],
      fixWebpackSourcePaths: true
    },
    reporters: ['progress', 'kjhtml'],
    port: 9876,
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: true,
    browsers: ['Chrome'],
    singleRun: false,
    restartOnFileChange: true
  });
};
```
> *As of Angular version 12 and later, the Angular CLI no longer generates a separate karma.conf.js file by default for configuring Karma. Instead, it uses a different configuration mechanism that simplifies the testing setup and aligns better with modern JavaScript and TypeScript development practices. Although it can be possible to add custom karma.config.js file here:* ```ng generate config karma```

This will create new config file and add config info in ***angular.json***

```json
{
  "projects": {
    "your-project-name": {
      "architect": {
        "test": {
          "options": {
            "karmaConfig": "./karma.conf.js",
            "codeCoverage": true
            // Other Karma configuration options here
          }
        }
      }
    }
  }
}
```

### tsconfig.spec.json
Defines testable typescript file configurations.
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/spec",
    "types": [
      "jasmine"
    ]
  },
  "include": [
    "src/**/*.spec.ts",
    "src/**/*.d.ts"
  ]
}
```

### __.spec.ts
Main test file where tests should be written. Every angular building block has a .spec file with post fixing the .spec.ts name.
Barebone .spec.ts file looks like this:
```ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { DashboardComponent } from './dashboard.component';

describe('DashboardComponent', () => {
  let component: DashboardComponent;
  let fixture: ComponentFixture<DashboardComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ DashboardComponent ]
    }).compileComponents();
    fixture = TestBed.createComponent(DashboardComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

# Jasmine Basics
## describe
Create a group of specs (often called a suite). The describe function is for grouping related specs, typically each test file has one at the top level. The string parameter is for naming the collection of specs, and will be concatenated with specs to make a spec's full name. This aids in finding specs in a large suite. If you name them well, your specs read as full sentences in traditional BDD style.
```ts
describe(description: string, specDefinitions: () => void): void
```
1. ***description*** Textual description of the group
2. ***specDefinitions*** Function for Jasmine to invoke that will define inner suites a specs

```ts
describe('DashboardComponent', () => {
  let component: DashboardComponent;
  let fixture: ComponentFixture<DashboardComponent>;

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});

// A focused `describe`. If suites or specs are focused, only those that are focused will be executed.
fdescribe('DashboardComponent', () => {
  let component: DashboardComponent;
  let fixture: ComponentFixture<DashboardComponent>;

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});

// A temporarily disabled `describe`. Specs within an xdescribe will be marked pending and not executed.
xdescribe('DashboardComponent', () => {
  let component: DashboardComponent;
  let fixture: ComponentFixture<DashboardComponent>;

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

## beforeEach
Run some shared setup before each of the specs in the describe in which it is called.
```ts
beforeEach(action: jasmine.ImplementationCallback, timeout?: number)
```
1.	action Function that contains the code to set up your specs.
2.	timeout Custom timeout for an async beforeEach.

```ts
beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ DashboardComponent ]
    })
    .compileComponents();

    fixture = TestBed.createComponent(DashboardComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });
```

## afterEach
Run some shared teardown after each of the specs in the describe in which it is called. Common use case can be resetting the state.
```ts
afterEach(async () => {
    fixture = null;
    component = null;
  });
```
## beforeAll
Run some shared setup once before all of the specs in the describe are run.

*Note: Be careful, sharing the setup from a beforeAll makes it easy to accidentally leak state between your specs so that they erroneously pass or fail.*
```ts
beforeAll(action: jasmine.ImplementationCallback, timeout?: number)
```
1.	action Function that contains the code to setup your specs.
2.	timeout Custom timeout for an async beforeAll.
```ts
beforeAll(async () => {
    data = [
      'Hello',
      'world'
    ]
  });
```

## afterAll
Run some shared teardown once after all of the specs in the describe are run.

*Note: Be careful, sharing the teardown from a afterAll makes it easy to accidentally leak state between your specs so that they erroneously pass or fail.*
```ts
afterAll(action: jasmine.ImplementationCallback, timeout?: number)
```
1.	action Function that contains the code to teardown your specs.
2.	timeout Custom timeout for an async afterAll
```ts
afterAll(async () => {
    data = null
  });
```
## it
Define a single spec. A spec should contain one or more expectations that test the state of the code.
A spec whose expectations all succeed will be passing and a spec with any failures will fail.
```ts
it(expectation: string, assertion?: jasmine.ImplementationCallback, timeout?: number)
```
1.	expectation Textual description of what this spec is checking
2.	assertion Function that contains the code of your test. If not provided the test will be pending.
3.	timeout Custom timeout for an async spec.
```ts
it('should create', () => {
    expect(component).toBeTruthy();
  });

// A focused `it`. If suites or specs are focused, only those that are focused will be executed.
fit('should create', () => {
    expect(component).toBeTruthy();
  });


// A temporarily disabled `it`. The spec will report as pending and will not be executed.
xit('should create', () => {
    expect(component).toBeTruthy();
  });
```
## expect
Create an expectation for a spec.
```ts
function expect<T>(actual: T): jasmine.Matchers<T>
function expectAsync<T, U>(actual: T | PromiseLike<T>): jasmine.AsyncMatchers<T, U>
```
1. actual Actual computed value to test expectations against.
```ts
expect(component).toBeTruthy();
expect(queryRefRefetchSpy).toHaveBeenCalled();
expect(queryVariables.take).toEqual(pageSize);
expect(queryRefRefetchSpy).toHaveBeenCalledOnceWith(queryVariables);
await expectAsync(
   client.getOffers({ consumerId: "test123" })
  ).toBeRejectedWith(new Error(Messages.REQUEST_REQUIRED));
```
## matchers
Matchers are the JavaScript function that does a Boolean comparison between an actual output and an expected output.

### Inbuilt Matcher

#### toBeTruthy
expect the actual value to be truthy.
```ts
expect(thing).toBeTruthy();
```

#### toEqual(expected)
expect the actual value to be equal to the expected, using deep equality comparison.
```ts
expect(bigObject).toEqual({"foo": ['bar', 'baz']});
```

#### toHaveBeenCalled()
expect the actual (a Spy) to have been called.
```ts
expect(mySpy).toHaveBeenCalled();
```

#### not
Invert the matcher following this expect
```ts
expect(something).not.toBe(true);
```

#### toBe(expected)
expect the actual value to be === to the expected value.
```ts
expect(result).toBeGreaterThan(3);
```

#### toBeGreaterThan(expected)
expect the actual value to be greater than the expected value.
```ts
expect(result).toBeGreaterThan(3);
```

#### toBeGreaterThanOrEqual(expected)
expect the actual value to be greater than or equal to the expected value.
```ts
expect(thing).toBeGreaterThanOrEqual(2);
```

#### toHaveBeenCalledWith()
expect the actual (a Spy) to have been called with particular arguments at least once.
```ts
expect(mySpy).toHaveBeenCalledWith('foo', 'bar', 2);
```

More at: https://jasmine.github.io/api/edge/matchers.html

### Custom Matchers
Custom matcher needs to be defined explicitly()
```ts
describe('This custom matcher example', function() {
   
   beforeEach(function() { 
      // We should add custom matched in beforeEach() function. 
      jasmine.addMatchers ({ 
         validateAge: function() { 
            Return {    
               compare: function(actual,expected) {
                  var result = {}; 
                  result.pass = (actual > = 13 && actual < = 19);
                  result.message = 'sorry u are not a teen ';
                  return result; 
               }   
            };   
         }    
      });    
   }); 
    
   it('Lets see whether u are teen or not', function() { 
      var myAge = 14; 
      expect(myAge).validateAge();         
   });
});
```

# Angular + Jasmine

## TestBed
Configures and initializes the environment for unit testing and provides methods for creating components and services in unit tests. TestBed gives a way to create components, handle injection, test asynchronous behavior and interact with our application.
```ts
configureTestingModule(moduleDef: TestModuleMetadata): TestBed;
```

```ts
@NgModule({
  declarations: [
    AuthorListComponent,
  ],
  imports: [
    CommonModule,
    ReactiveFormsModule,
  ],
  providers: []
})
export class AuthorsModule { }
```

```ts
TestBed.configureTestingModule({
      declarations: [AuthorListComponent],
      imports: [PaginationModule, RouterTestingModule],
      providers: [],
    }).compileComponents();
```
```ts
compileComponents(): Promise<any>
```
Calling **compileComponents()** is needed only when our component is using templateUrl and/or styleUrls.

## Fixture
Fixture for debugging and testing a component.

```ts
let fixture: ComponentFixture<HeaderComponent>;
fixture = TestBed.createComponent(HeaderComponent);
```
## Change Detection
```ts
it('should display notification container when notification button clicked', () => {
    const notificationBtn = nativeElement.querySelector('#notification-btn') as HTMLElement;
    notificationBtn.click();
    fixture.detectChanges();
    const notificationContainer = nativeElement.querySelector('#notification-container');
    expect(notificationContainer).toBeTruthy();
  });
```
```ts fixture.detectChanges();``` Trigger a change detection cycle for the component.

## Native Element
The native element at the root of the component.
```ts
let nativeElement: HTMLElement;
nativeElement = fixture.nativeElement as HTMLElement;
const notificationBtn = nativeElement.querySelector('#notification-btn') as HTMLElement;
    notificationBtn.click();
```

## Debug Element
DebugElement is a wrapper across native elements and tested component allowing test to run on all supported platforms.
```ts
const debugElement = fixture.debugElement;
const h1 = debugElement.query(By.css('h1'));
const userElements = debugElement.queryAll(By.css('.user'));
```
```ts fixture.debugElement``` has other methods and properties that are useful in tests like By.css() for example.

## By
```ts
Predicates for use with DebugElement's query functions.
const h1 = debugElement.query(By.css('h1'));
const userElements = debugElement.queryAll(By.css('.user'));
```

```ts
By.all()
By.css('.green')
By.css('#id')
By.directive(FormControlName)
```

## Accessing non public members
```ts
it('should return 5 if input 3 and 2', () => {
    component['total'] = 2;
    let result = component['add'](3);
    expect(result).toEqual(5);
});
```

## Testing Async Operations
```ts
export class AuthService {
  isAuthenticated(): Promise<boolean> {
    return Promise.resolve(!!localStorage.getItem('token'));
  }
}
```
```ts
export class LoginComponent implements  OnInit {

  needsLogin: boolean = true;

  constructor(private auth: AuthService) {
  }

  ngOnInit()  {
    this.auth.isAuthenticated().then((authenticated) => {
      this.needsLogin = !authenticated;
    })
  }
}
```
```ts
it('Button label via jasmine.done', () => {
    fixture.detectChanges();
    expect(el.nativeElement.textContent.trim()).toBe('Login');
    spyOn(authService, 'isAuthenticated').and.returnValue(Promise.resolve(true));
    component.ngOnInit();
    fixture.detectChanges();
    expect(el.nativeElement.textContent.trim()).toBe('Logout');
  });
```
### Jasmine’s done Function
```ts
it('Button label via jasmine.done', (done) => { (1)
  fixture.detectChanges();
  expect(el.nativeElement.textContent.trim()).toBe('Login');
  let spy = spyOn(authService, 'isAuthenticated').and.returnValue(Promise.resolve(true));
  component.ngOnInit();
  spy.calls.mostRecent().returnValue.then(() => { (2)
    fixture.detectChanges();
    expect(el.nativeElement.textContent.trim()).toBe('Logout');
    done(); (3)
  });
});
```

### async and whenStable
```ts
it('Button label via async() and whenStable()', async(() => { (1)
  fixture.detectChanges();
  expect(el.nativeElement.textContent.trim()).toBe('Login');
  spyOn(authService, 'isAuthenticated').and.returnValue(Promise.resolve(true));
  fixture.whenStable().then(() => { (2)
    fixture.detectChanges();
    expect(el.nativeElement.textContent.trim()).toBe('Logout');
  });
  component.ngOnInit();
}));
```

### fakeAsync and tick
```ts
it('Button label via fakeAsync() and tick()', fakeAsync(() => { (1)
  expect(el.nativeElement.textContent.trim()).toBe('');
  fixture.detectChanges();
  expect(el.nativeElement.textContent.trim()).toBe('Login');
  spyOn(authService, 'isAuthenticated').and.returnValue(Promise.resolve(true));
  component.ngOnInit();

  tick(); (2)
  fixture.detectChanges();
  expect(el.nativeElement.textContent.trim()).toBe('Logout');
}));
```

# Mocking & Spying

## Testing with the Concrete implementation
```ts
describe('Component: Login', () => {

  let component: LoginComponent;
  let service: AuthService;

  beforeEach(() => { (1)
    service = new AuthService();
    component = new LoginComponent(service);
  });

  afterEach(() => { (2)
    localStorage.removeItem('token');
    service = null;
    component = null;
  });
});
```
- Not isolated
- WIll not be feasible if has cascade dependencies
- Tightly coupled

## Mocking with Fake Implementation
```ts
class MockAuthService { (1)
  authenticated = false;

  isAuthenticated() {
    return this.authenticated;
  }
}

describe('Component: Login', () => {

  let component: LoginComponent;
  let service: MockAuthService;

  beforeEach(() => { (2)
    service = new MockAuthService();
    component = new LoginComponent(service);
  });

  afterEach(() => {
    service = null;
    component = null;
  });
});
```
- Isolated
- Need more code

## Mocking with Inheritance
```ts
class MockAuthService extends AuthService {
  authenticated = false;

  isAuthenticated() {
    return this.authenticated;
  }
}
```
- Isolated
- Need more code

## Testing with Spy
Spy is a jasmine class to mock and simulate js objects. Spy object can be created with **spyOn**, **spyOnProperty**, **jasmine.createSpy**, or **jasmine.createSpyObj**.

### spyOn(obj, methodName)
Install a spy onto an existing object.
1.	object The object upon which to install the `Spy`.
2.	method The name of the method to replace with a `Spy`.

```ts
const queryRefRefetchSpy = spyOn<any>(component.queryRef, 'refetch');
expect(queryRefRefetchSpy).toHaveBeenCalled();

spyOn(component['gql'], 'fetch').and.returnValue(of(mockQueryResult));
```
### spyOnProperty(obj, propertyName, accessTypeopt)
Install a spy on a property installed with Object.defineProperty onto an existing object.
1.	object The object upon which to install the `Spy`.
2.	property The name of the property to replace with a `Spy`.
3.	accessType The access type (get|set) of the property to `Spy` on.
```ts
const spy = spyOnProperty(person, 'fullName').and.returnValue(
  'dummy stubbed name'
);
expect(person.fullName).toBe('dummy stubbed name');
```

### jasmine.createSpy(name, originalFn)
Create a bare Spy object. This won't be installed anywhere and will not have any implementation behind it. Can be used when there is no function to spy on.
1.	name	Name to give the spy. This will be displayed in failure messages.
2.	originalFn Function to act as the real implementation.
```ts
const o = {};
o.some = jasmine.createSpy('some');
```

### createSpyObj(baseName, methodNames, propertyNames)
Create an object with multiple Spies as its members. It’s used to create mocks that will spy on methods. It will return an object for each property defined in the spy.
1.	baseName Base name for the spies in the object.
2.	methodNames Array of method names to create spies for, or Object whose keys will be method names and values the returnValue.
3.	propertyNames Array of property names to create spies for, or Object whose keys will be propertynames and values the returnValue.
```ts
describe('MyComponent', () => {
const mockedUserService = jasmine.createSpyObj('MyRealService', ['getFirstName', 'getLastName', 'getId']);
beforeEach(() => TestBed.configureTestingModule({
providers: [{
      provide: UserService,
      useValue: mockedUserService
    }]
}));
});
```

# Angular in-built Testing modules

## RouterTestingModule
In-built testing module for RouterModule in angular.

```ts
describe('Router: App', () => {

  let location: Location;
  let router: Router;
  let fixture;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [RouterTestingModule.withRoutes(routes)],
      declarations: [
        HomeComponent,
        SearchComponent,
        AppComponent
      ]
    });

    router = TestBed.get(Router);
    location = TestBed.get(Location);

    fixture = TestBed.createComponent(AppComponent);
    router.initialNavigation();
  });
});

it('navigate to "" redirects you to /home', fakeAsync(() => { (1)
  router.navigate(['']); (2)
  tick(); (3)
  expect(location.path()).toBe('/home'); (4)
}));

it('navigate to "search" takes you to /search', fakeAsync(() => {
  router.navigate(['search']);
  tick();
  expect(location.path()).toBe('/search');
}));
```

## HttpClientTestingModule
Inbuilt testing module for HttpClientModule. The HttpClientTestingModule makes it easier to mock requests using the HttpTestingController service.

```ts
describe('GithubApiService', () => {
  let injector: TestBed;
  let service: GithubApiService;
  let httpMock: HttpTestingController;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [GithubApiService]
    });
    injector = getTestBed();
    service = injector.get(GithubApiService);
    httpMock = injector.get(HttpTestingController);
  });
});

it('should return an Observable<User[]>', () => {
    const dummyUsers = [
      { login: 'John' },
      { login: 'Doe' }
    ];

    service.getUsers().subscribe(users => {
      expect(users.length).toBe(2);
      expect(users).toEqual(dummyUsers);
    });

    const req = httpMock.expectOne(`${service.API_URL}/users`);
    expect(req.request.method).toBe("GET");
    req.flush(dummyUsers);
  });

afterEach(() => {
  httpMock.verify();
});
```
It's important to run `HttpTestingController.verify` to make sure that there are no outstanding requests.


# Testing Angular Building Blocks
## AAA
1.	Arrange inputs and targets. Arrange steps should set up the test case. Does the test require any objects or special settings? Does it need to prepare a database? Does it need to log into a web app? Handle all of these operations at the start of the test.
2.	Act on the target behavior. Act steps should cover the main thing to be tested. This could be calling a function or method, calling a REST API, or interacting with a web page. Keep actions focused on the target behavior.
3.	Assert expected outcomes. Act steps should elicit some sort of response. Assert steps verify the goodness or badness of that response. Sometimes, assertions are as simple as checking numeric or string values. Other times, they may require checking multiple facets of a system. Assertions will ultimately determine if the test passes or fails.

```ts
describe('DashboardComponent', () => {
  let component: DashboardComponent;
  let fixture: ComponentFixture<DashboardComponent>;

  beforeEach(() => {
//Arrange
    TestBed.configureTestingModule({
      declarations: [ DashboardComponent ]
    })
    .compileComponents();

    fixture = TestBed.createComponent(DashboardComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    //Act
    const hello = component.getHello()

    //Assert
    expect(hello).toBeTruthy();
  });
});
```


## Testing components

### Async TestBed
```ts
describe('DashboardComponent', () => {
  let component: DashboardComponent;
  let fixture: ComponentFixture<DashboardComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [ DashboardComponent ]
    })
      .compileComponents().then(() => {
        console.debug('beforeEach', new Date().getMilliseconds());
        fixture = TestBed.createComponent(DashboardComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
    });
  });

  it('should create', () => {
    console.debug('it', new Date().getMilliseconds());
    expect(component).toBeTruthy();
  });
});
```
![Async failed](/.imgs/async-testbed-1.png)

```ts
describe('DashboardComponent', () => {
  let component: DashboardComponent;
  let fixture: ComponentFixture<DashboardComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [DashboardComponent],
    }).compileComponents();

    console.debug('beforeEach', new Date().getMilliseconds());
    fixture = TestBed.createComponent(DashboardComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    console.debug('it', new Date().getMilliseconds());
    expect(component).toBeTruthy();
  });
});
```
![Async passed](/.imgs/async-testbed-2.png)

### DOM interaction
```html
<button id="notification-btn
   (click)=" isNotificationVisible=!isNotificationVisible">
  <span class="material-symbols-sharp"> notifications </span>
</button>

<div id="notification-container"
  *ngIf="isNotificationVisible"></div>
```
```ts
fit('should dispay notification container when notification button clicked', () => {
    const notificationBtn = nativeElement.querySelector('#notification-btn') as HTMLElement;
    notificationBtn.click();
    const notificationContainer = nativeElement.querySelector('#notification-container');
    expect(notificationContainer).toBeTruthy();
  });
```
![Dom interaction](/.imgs/dom-interaction-1.png)

```ts
fit('should dispay notification container when notification button clicked', () => {
    const notificationBtn = nativeElement.querySelector('#notification-btn') as HTMLElement;
    notificationBtn.click();
    fixture.detectChanges(); // Magic spell
    const notificationContainer = nativeElement.querySelector('#notification-container');
    expect(notificationContainer).toBeTruthy();
  });
```

![Dom interaction](/.imgs/dom-interaction-2.png)