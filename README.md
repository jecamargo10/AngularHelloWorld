## Angular Tutorial

 1. Install angular globally
```
npm install -g @angular/cli
```
2. Create new angular front
```
 ng new front 
```
3. Test the local deployment
```
ng serve --host 0.0.0.0 --port 8080
```
4. Edit the file src/app/app.module.ts 	
  4.1 Add the following imports
  ```
import { HttpClientModule } from '@angular/common/http';
import { ReactiveFormsModule } from '@angular/forms';
```
4.2 Add that module in `@NgModule` imports.
```
  imports: [
    BrowserModule,
    HttpClientModule,
    ReactiveFormsModule,
    AppRoutingModule,
    …
  ]
```
The modulo HttpClient should be available in angular
5. While angular is stop (stop with control+C) create the angular service
```
ng g service rest
```
6. Edit the file with the name of the service ( in my case: rest.services.ts) 
```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class RestService {

  constructor(private http: HttpClient) { }

  rootURL = '/api';

  getUsers() {
    return this.http.get(this.rootURL + '/users');
  }

  addUser(user: any) {
    return this.http.post(this.rootURL + '/user', {user});
  }

}
```
7. Replace the app.component.ts
```
import { Component, OnDestroy } from '@angular/core';
import { FormGroup, FormControl, Validators } from '@angular/forms';
import { RestService } from './rest.service';
import { takeUntil } from 'rxjs/operators';
import { Subject } from 'rxjs';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnDestroy {

  constructor(private appService: RestService) {}

  title = 'angular-nodejs-example';

  userForm = new FormGroup({
    firstName: new FormControl('', Validators.nullValidator && Validators.required),
    lastName: new FormControl('', Validators.nullValidator && Validators.required),
    email: new FormControl('', Validators.nullValidator && Validators.required)
  });

  users: any[] = [];
  userCount = 0;

  destroy$: Subject<boolean> = new Subject<boolean>();

  onSubmit() {
    this.appService.addUser(this.userForm.value).pipe(takeUntil(this.destroy$)).subscribe(data => {
      console.log('message::::', data);
      this.userCount = this.userCount + 1;
      console.log(this.userCount);
      this.userForm.reset();
    });
  }

  ngOnDestroy() {
    this.destroy$.next(true);
    this.destroy$.unsubscribe();
  }
}
```
8. Replace the app.component.html
```html
<div class="container">
  <div class="row">
    <div class="col-md-7 mrgnbtm">
      <h2>Create User</h2>
      <form [formGroup]="userForm" (ngSubmit)="onSubmit()">
        <div class="row">
          <div class="form-group col-md-6">
            <label for="exampleInputEmail1">First Name</label>
            <input type="text" class="form-control" formControlName="firstName" id="exampleInputEmail1" aria-describedby="emailHelp" placeholder="First Name">
          </div>
          <div class="form-group col-md-6">
            <label for="exampleInputPassword1">Last Name</label>
            <input type="text" class="form-control" formControlName="lastName" id="exampleInputPassword1" placeholder="Last Name">
          </div>
        </div>
        <div class="row">
          <div class="form-group col-md-12">
            <label for="exampleInputEmail1">Email</label>
            <input type="text" class="form-control" formControlName="email" id="exampleInputEmail1" aria-describedby="emailHelp" placeholder="Email">
          </div>
        </div>
        <button type="submit" [disabled]="!userForm.valid" class="btn btn-danger">Create</button>
      </form>
    </div>
    <div class="col-md-4 mrgnbtm">
      <div class="number">
        {{userCount}}
      </div>

    </div>
  </div>
</div>
```

9. For proxy between angular and nodes in development define the following file proxy.conf.json in main angular folder
```
{
  "/api": {
    "target": "http://localhost:3080",
    "secure": false
  }
}
```
10. Edit the file angular.json under the architect:server:options, add the proxyConfig for routing
```
        "serve": {
          "builder": "@angular-devkit/build-angular:dev-server",
          "options": {
            "browserTarget": "front:build",
            "proxyConfig": "proxy.conf.json"

          },
```
11. Test
```
npm run dev   
```

