# Angular Unit Testing with Jasmine and Karma

## Table of Contents
- [Introduction](#introduction)
- [Testing Environment Setup](#testing-environment-setup)
- [Basic Test Structure](#basic-test-structure)
- [Testing Components](#testing-components)
- [Testing Services](#testing-services)
- [Testing HTTP Requests](#testing-http-requests)
- [Testing Routing](#testing-routing)
- [Advanced Testing Techniques](#advanced-testing-techniques)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

## Introduction

### Using Console Logs to Generate Mock Data for Unit Tests (Developers guide)

Developers can easily write unit test cases by gathering mock data using console logs during development. This approach provides a practical way to capture realistic data for your tests with minimal extra effort.

### The Workflow

When developing features, you can strategically add console logs to capture the exact data you need for your tests:

1. **Log during development**: Add `console.log()` statements at key points where you need data for tests
2. **Copy the output**: The logged data becomes your test fixture
3. **Format for tests**: Paste the logged data into your test file as mock input/expected output

### Benefits

This method has several advantages:
- Ensures your mock data matches real application data
- Reduces time spent creating fictional test data
- Makes tests more realistic and comprehensive
- Simplifies test maintenance

### Example Implementation

Let's say you're developing a user authentication function. Here's how you could apply this approach:

```javascript
function authenticateUser(credentials) {
  // Log the input for use in test cases
  console.log('Test input:', JSON.stringify(credentials));
  
  // Authentication logic here
  const result = {
    success: true,
    token: 'abc123',
    user: { id: 42, name: 'Jane Smith' }
  };
  
  // Log the result for expected output in tests
  console.log('Expected output:', JSON.stringify(result));
  
  return result;
}
```

Then in your test file:

```javascript
describe('User Authentication', () => {
  it('should authenticate valid credentials', () => {
    // Mock data copied from console logs
    const testInput = {
      username: 'jsmith',
      password: 'securepass'
    };
    
    const expectedOutput = {
      success: true,
      token: 'abc123',
      user: { id: 42, name: 'Jane Smith' }
    };
    
    const result = authenticateUser(testInput);
    expect(result).toEqual(expectedOutput);
  });
});
```

### Why Test Angular Applications?

Unit testing is a critical part of modern development workflows that helps:
- Catch bugs early in the development cycle
- Document and verify expected behavior
- Facilitate safe refactoring
- Improve code quality and design
- Provide a safety net when making changes

## Testing Environment Setup

### Key Configuration Files

- **karma.conf.js**: Configures Karma settings including browsers, reporters, etc.
- **test.ts**: Main entry point for tests
- **tsconfig.spec.json**: TypeScript configuration for testing

### Running Tests

```bash
# The choice between using npx or yarn depends on how your Angular project is set up and what package manager you're using

# Runs all unit tests using Karma (the default Angular test runner)
npx ng test

# Run tests once
ng test --watch=false

# Run tests continuously (watches for changes)
ng test --watch=true

# Generate coverage report
ng test --code-coverage

# Run test in headless
npx ng test --watch=true --browsers=ChromeHeadless

```

### Command to run all unit test cases

For running all unit test cases contiounsly with code coverage:

```bash
npx ng test --watch=true --code-coverage
```

### Command to run a single spec file

For running a single spec file contiounsly with code coverage:

```bash
npx ng test --include=relative/file/path.ts --watch=true --code-coverage

# Example:
npx ng test --include=src/client/app/shared/retrievebooking-service/retrievebooking-service.spec.ts --watch=true --code-coverage

```

## Basic Test Structure

### Jasmine Basics

Jasmine uses a behavior-driven development (BDD) syntax:

```typescript
describe('Component Name', () => {
  // Suite: Group of related tests
  
  beforeEach(() => {
    // Setup code that runs before each test
  });
  
  afterEach(() => {
    // Cleanup code that runs after each test
  });
  
  it('should do something specific', () => {
    // Test case with expectation
    expect(actualValue).toBe(expectedValue);
  });
});
```

### Common Jasmine Matchers

```typescript
expect(value).toBe(exactValue);          // Strict equality (===)
expect(value).toEqual(expectedObject);   // Deep equality for objects
expect(value).toBeTruthy();              // Checks if value is truthy
expect(value).toBeFalsy();               // Checks if value is falsy
expect(array).toContain(item);           // Checks if array contains item
expect(fn).toThrow();                    // Checks if function throws error
expect(spy).toHaveBeenCalled();          // Checks if spy was called
expect(spy).toHaveBeenCalledWith(args);  // Checks spy call arguments
```

### Angular TestBed

TestBed creates a testing module environment:

```typescript
import { TestBed } from '@angular/core/testing';
import { MyComponent } from './my.component';
import { MyService } from './my.service';

describe('MyComponent', () => {
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [MyComponent],
      providers: [MyService]
    }).compileComponents();
  });
  
  it('should create the component', () => {
    const fixture = TestBed.createComponent(MyComponent);
    const component = fixture.componentInstance;
    expect(component).toBeTruthy();
  });
});
```

### Angular test case template for component (verteil-ui-template)

TestBed creates a testing module environment:

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { TranslateModule } from "@ngx-translate/core";
import { HttpClientTestingModule } from "@angular/common/http/testing";
import { MatLegacyDialogModule } from "@angular/material/legacy-dialog";
import { MyComponent } from './my.component';
import { MyService } from './my.service';

describe('componentClassName', (): void => {
  let component: MyComponent;
  let fixture: ComponentFixture<MyComponent>;

  beforeEach(async (): Promise<void> => {
    await TestBed.configureTestingModule({
      declarations: [MyComponent],
      imports: [TranslateModule.forRoot()],
      providers: [],
    }).compileComponents();
  });

  beforeEach((): void => {
    fixture = TestBed.createComponent(MyComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create the component', (): void => {
    expect(component).toBeTruthy();
  });
});

```

## Testing Components

### Basic Component Testing

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { MyComponent } from './my.component';

describe('MyComponent', () => {
  let component: MyComponent;
  let fixture: ComponentFixture<MyComponent>;
  
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [MyComponent],
      providers: []
    }).compileComponents();
    
    fixture = TestBed.createComponent(MyComponent);
    component = fixture.componentInstance;
    fixture.detectChanges(); // Initial binding
  });
  
  it('should create', () => {
    expect(component).toBeTruthy();
  });
  
  it('should toggle showBreakup from false to true', () => {
    component.showBreakup = false;
    component.toggleSplitpnrdata();
    expect(component.showBreakup).toBeTrue();
  });
});
```

### Testing Component Inputs and Outputs

```typescript
it('should respond to input changes', () => {
  component.inputProperty = 'new value';
  fixture.detectChanges();
  // Assert changes based on input
});

it('should emit event when button clicked', () => {
  spyOn(component.outputEvent, 'emit');
  
  const button = fixture.debugElement.query(By.css('button'));
  button.triggerEventHandler('click', null);
  
  expect(component.outputEvent.emit).toHaveBeenCalled();
});
```

### Testing Component with Dependencies

```typescript
import { MyComponent } from './my.component';
import { MyService } from './my.service';

// Create a mock service

let mockService: jasmine.SpyObj<MyService>;

describe('MyComponent with dependencies', () => {

  mockService = jasmine.createSpyObj('MyService', ['getData']);

  mockService.getData.and.returnValue('mocked data');

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [MyComponent],
      providers: [{ provide: MyService, useValue: mockService }]
    }).compileComponents();
  });

  beforeEach((): void => {
    fixture = TestBed.createComponent(AddReviewComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });
  
  it('should create the component', (): void => {
    expect(component).toBeTruthy();
  });
});
```

### Testing DOM Interactions

```typescript
it('should update model when input value changes', () => {
  const inputElement = fixture.debugElement.query(By.css('input')).nativeElement;
  
  inputElement.value = 'new value';
  inputElement.dispatchEvent(new Event('input'));
  
  expect(component.inputValue).toBe('new value');
});

it('should call method when button is clicked', () => {
  spyOn(component, 'onButtonClick');
  
  const button = fixture.debugElement.query(By.css('button')).nativeElement;
  button.click();
  
  expect(component.onButtonClick).toHaveBeenCalled();
});
```

## Testing Services

### Testing Simple Services

```typescript
import { TestBed } from '@angular/core/testing';
import { MyService } from './my.service';

describe('MyService', () => {
  let service: MyService;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [MyService]
    });
    
    service = TestBed.inject(MyService);
  });
  
  it('should be created', () => {
    expect(service).toBeTruthy();
  });
  
  it('should calculate correctly', () => {
    const result = service.calculate(2, 3);
    expect(result).toBe(5);
  });
});
```

### Testing Services with Dependencies

```typescript
import { TestBed } from '@angular/core/testing';
import { MyService } from './my.service';
import { DependencyService } from './dependency.service';

describe('MyService with dependencies', () => {
  let service: MyService;
  let dependencyServiceSpy: jasmine.SpyObj<DependencyService>;
  
  beforeEach(() => {
    dependencyServiceSpy = jasmine.createSpyObj('DependencyService', ['getData']);
    
    TestBed.configureTestingModule({
      providers: [
        MyService,
        { provide: DependencyService, useValue: dependencyServiceSpy }
      ]
    });
    
    service = TestBed.inject(MyService);
  });
  
  it('should use dependency service', () => {
    dependencyServiceSpy.getData.and.returnValue('test data');
    
    const result = service.processData();
    
    expect(dependencyServiceSpy.getData).toHaveBeenCalled();
    expect(result).toBe('PROCESSED: test data');
  });
});
```

## Testing HTTP Requests

### Setup for HTTP Testing

```typescript
import { TestBed } from '@angular/core/testing';
import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';
import { DataService } from './data.service';

describe('DataService', () => {
  let service: DataService;
  let httpMock: HttpTestingController;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [DataService]
    });
    
    service = TestBed.inject(DataService);
    httpMock = TestBed.inject(HttpTestingController);
  });
  
  afterEach(() => {
    httpMock.verify(); // Verify no outstanding requests
  });
  
  it('should get users', () => {
    const mockUsers = [{ id: 1, name: 'John' }, { id: 2, name: 'Jane' }];
    
    service.getUsers().subscribe(users => {
      expect(users).toEqual(mockUsers);
    });
    
    const req = httpMock.expectOne('api/users');
    expect(req.request.method).toBe('GET');
    req.flush(mockUsers); // Return mock data
  });
  
  it('should handle errors', () => {
    service.getUsers().subscribe(
      () => fail('should have failed'),
      (error) => {
        expect(error.status).toBe(404);
      }
    );
    
    const req = httpMock.expectOne('api/users');
    req.flush('Not Found', { status: 404, statusText: 'Not Found' });
  });
});
```

### Testing POST, PUT, DELETE Requests

```typescript
it('should create user', () => {
  const newUser = { name: 'New User' };
  const mockResponse = { id: 3, name: 'New User' };
  
  service.createUser(newUser).subscribe(response => {
    expect(response).toEqual(mockResponse);
  });
  
  const req = httpMock.expectOne('api/users');
  expect(req.request.method).toBe('POST');
  expect(req.request.body).toEqual(newUser);
  req.flush(mockResponse);
});

it('should update user', () => {
  const user = { id: 1, name: 'Updated Name' };
  
  service.updateUser(user).subscribe(response => {
    expect(response).toEqual(user);
  });
  
  const req = httpMock.expectOne(`api/users/${user.id}`);
  expect(req.request.method).toBe('PUT');
  expect(req.request.body).toEqual(user);
  req.flush(user);
});

it('should delete user', () => {
  const userId = 1;
  
  service.deleteUser(userId).subscribe(response => {
    expect(response).toBeTruthy();
  });
  
  const req = httpMock.expectOne(`api/users/${userId}`);
  expect(req.request.method).toBe('DELETE');
  req.flush({});
});
```

## Testing Routing

### Testing Router Navigation

```typescript
import { TestBed } from '@angular/core/testing';
import { Router } from '@angular/router';
import { RouterTestingModule } from '@angular/router/testing';
import { NavComponent } from './nav.component';

describe('NavComponent', () => {
  let component: NavComponent;
  let routerSpy: jasmine.SpyObj<Router>;
  
  beforeEach(async () => {
    routerSpy = jasmine.createSpyObj('Router', ['navigate']);

    await TestBed.configureTestingModule({
      imports: [RouterTestingModule],
      declarations: [NavComponent],
      providers: [
        { provide: Router, useValue: routerSpy },
      ]
    }).compileComponents();
    
    const fixture = TestBed.createComponent(NavComponent);
    component = fixture.componentInstance;
    router = TestBed.inject(Router);
    fixture.detectChanges();
  });
  
  it('should navigate when button clicked', () => {
    routerSpy.navigate.and.callThrough();
    
    component.navigateToPage('about');
    
    expect(routerSpy.navigate).toHaveBeenCalledWith(['/about']);
  });
});
```

## Advanced Testing Techniques

### Testing Asynchronous Code

```typescript
// Using done callback
it('should update value after delay using done', (done) => {
  component.value = 'initial';

  // Simulate delayed update
  setTimeout(() => {
    component.value = 'updated';
    expect(component.value).toBe('updated');
    done(); // Notify Jasmine the async task is complete
  }, 1000);

  expect(component.value).toBe('initial');
});

// Using fakeAsync/tick
import { fakeAsync, tick } from '@angular/core/testing';

it('should update after timeout', fakeAsync(() => {
  component.delayedUpdate();
  
  expect(component.value).toBe('initial');
  
  tick(1000); // Simulate passage of time (1000ms)
  
  expect(component.value).toBe('updated');
}));
```

### Testing with Observables

```typescript
import { of, throwError } from 'rxjs';

it('should handle observable data', () => {
  spyOn(service, 'getData').and.returnValue(of(['item1', 'item2']));
  
  component.loadData();
  
  expect(component.items.length).toBe(2);
  expect(component.items).toContain('item1');
});

it('should handle observable error', () => {
  spyOn(service, 'getData').and.returnValue(throwError('Error'));
  spyOn(console, 'error');
  
  component.loadData();
  
  expect(component.items).toEqual([]);
  expect(component.error).toBe('Error');
  expect(console.error).toHaveBeenCalled();
});
```

### Testing Forms

```typescript
import { ReactiveFormsModule, FormBuilder } from '@angular/forms';

describe('FormComponent', () => {
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [ReactiveFormsModule],
      declarations: [FormComponent]
    }).compileComponents();
  });
  
  it('should validate form correctly', () => {
    const fixture = TestBed.createComponent(FormComponent);
    const component = fixture.componentInstance;
    fixture.detectChanges();
    
    // Form is initially invalid
    expect(component.form.valid).toBeFalsy();
    
    // Set form values
    component.form.controls.name.setValue('John');
    component.form.controls.email.setValue('invalid');
    
    // Email validation should fail
    expect(component.form.controls.email.valid).toBeFalsy();
    
    // Fix email
    component.form.controls.email.setValue('john@example.com');
    
    // Form should now be valid
    expect(component.form.valid).toBeTruthy();
  });
  
  it('should call onSubmit when form is submitted', () => {
    const fixture = TestBed.createComponent(FormComponent);
    const component = fixture.componentInstance;
    fixture.detectChanges();
    
    spyOn(component, 'onSubmit');
    
    component.form.controls.name.setValue('John');
    component.form.controls.email.setValue('john@example.com');
    
    const form = fixture.debugElement.query(By.css('form')).nativeElement;
    form.dispatchEvent(new Event('submit'));
    
    expect(component.onSubmit).toHaveBeenCalled();
  });
});
```

## Best Practices

### Test File Organization

```
src/
└── app/
    └── components/
        ├── my-component/
        │   ├── my-component.component.ts
        │   ├── my-component.component.html
        │   ├── my-component.component.css
        │   └── my-component.component.spec.ts  // Test file
        └── ...
```

### Naming Conventions

- Test file: `*.spec.ts`
- Test suite: `describe('ComponentName', () => {...})`
- Test case: `it('should do something specific', () => {...})`

### Mocking Strategy

1. **Mock only what you need**:
   - Isolate the unit being tested
   - Mock external dependencies
   - Mock expensive operations

2. **Use appropriate mocking techniques**:
   - Jasmine spies for function mocking
   - Mock services with `jasmine.createSpyObj`
   - Simple object literals for data models

### Code Coverage

Run tests with coverage report:

```bash
ng test --code-coverage
```

Coverage goals:
- Aim for at least 80% code coverage
- Focus on critical paths and edge cases
- Avoid testing just for coverage metrics

### Test Readability

- **Arrange, Act, Assert**:
  ```typescript
  it('should increment counter', () => {
    // Arrange
    const initialValue = component.counter;
    
    // Act
    component.increment();
    
    // Assert
    expect(component.counter).toBe(initialValue + 1);
  });
  ```

- **Clear test descriptions**:
  ```typescript
  // Good
  it('should display error message when input is invalid', () => {});
  
  // Bad
  it('test input validation', () => {});
  ```

### Test Isolation

- Each test should be independent
- Reset state between tests using `beforeEach`
- Avoid shared state that can cause test interference

## Troubleshooting

### Common Testing Issues

1. **Test Fails Intermittently**:
   - Check for async operations not properly handled
   - Look for shared state between tests
   - Verify timing issues with `fakeAsync` and `tick`

2. **Component Changes Not Reflected**:
   - Ensure `fixture.detectChanges()` is called after changes
   - Check if change detection is running properly

3. **Cannot Find Element in DOM**:
   - Verify the element exists in the template
   - Check CSS selector used in `fixture.debugElement.query`
   - Ensure component is fully initialized

4. **Dependency Injection Errors**:
   - Make sure all dependencies are provided in the test module
   - Check for circular dependencies
   - Verify the correct providers are registered

5. **HTTP Testing Issues**:
   - Ensure `HttpClientTestingModule` is imported
   - Verify expectations using `httpMock.expectOne`
   - Call `httpMock.verify()` to check for unexpected requests

### Debugging Tests

1. **Use console.log**:
   ```typescript
   it('should debug test state', () => {
     console.log('Component state:', component);
     // Test assertions
   });
   ```

2. **Inspect DOM elements**:
   ```typescript
   it('should debug DOM', () => {
     const element = fixture.debugElement.query(By.css('div')).nativeElement;
     console.log('Element HTML:', element.outerHTML);
   });
   ```

3. **Use browser developer tools**:
   - Open Karma in debug mode
   - Use browser breakpoints
   - Step through code execution

4. **Test in isolation**:
   - Use `fdescribe` or `fit` to focus on specific tests
   - Use `xdescribe` or `xit` to exclude tests

### Continuous Integration

For CI environments, run tests in headless mode:

```bash
ng test --watch=false --browsers=ChromeHeadless
```

Configure Karma for CI in karma.conf.js:

```javascript
module.exports = function(config) {
  config.set({
    // ... other settings
    browsers: ['ChromeHeadless'],
    singleRun: true,
    reporters: ['progress', 'junit'],
    junitReporter: {
      outputDir: 'test-results',
      outputFile: 'test-results.xml'
    }
  });
};
```

---

This documentation should serve as a comprehensive guide for your team to understand and implement effective unit tests in your Angular applications using Jasmine and Karma. Remember that good tests lead to more maintainable, robust, and reliable code.
