# Django Testing

some example for views uesd to manage and control users in django...

first you will need to import this libraries:

```python
from rest_framework import viewsets
from .serializers import TestSerializer
from .models import TestH
from rest_framework.decorators import api_view
from django.http import HttpResponse
from django.contrib.auth.models import User
from django.core import serializers
from django.contrib.auth import authenticate, login, logout
```

## signup with new user
this is done with the following steps:

 - get user profile data( username, password, email ).
 - create a new user instance by using( `User.objects.create_user` )
 - save this user.

```python
@api_view(['POST'])  
def signup_user(request):  
    u = request.query_params.get('user')  
    p = request.query_params.get('pass')  
    e = request.query_params.get('email')  
    user = User.objects.create_user(u, e, p)  
    user.save()  
    return HttpResponse(u + ' is added!!')
```

## login
this is done with the following steps:

 - get user profile data( username, password ).
 - get a user by using ( `authenticate(username, password)` ) and save is in a var
 - if the user is saved in the system and is authenticate we should have the user in the var.
 - log in by using ( `login` )

```python
@api_view(['POST'])  
def login_user(request):  
    u = request.query_params.get('user')  
    p = request.query_params.get('pass')  
    user = authenticate(username=u, password=p)  
    if user is not None:  
        login(request, user)  
        return HttpResponse(request.user.username)  
    else:  
        return HttpResponse('Error!!')
```

## logout
this is done with the following steps:

 - just logout :\ ( `logout` ).

```python
def logout_user(request):  
    logout(request)  
    return HttpResponse('logged out')
```


## get all the users in the system
this is done with the following steps:

 - get all users by using( `User.objects.all()` ).
 - serialize the data by using ( `serializers.serialize('json', (data)` ) this will save the data as json

```python
def get_accounts(request):  
    qs = User.objects.all()  
    qs_json = serializers.serialize('json', qs)  
    return HttpResponse(qs_json, content_type='application/json')
```

## get logged in user data
this is done with the following steps:

 - get user profile data by using ( `request.user` ).
 - get a username by using ( `request.user.username` ).
 - if the user in not login this view will return an empty response

```python
def get_me(request):  
    return HttpResponse(request.user.username)
```

## change_user_password
this is done with the following steps:

 - get user profile data( username, old_password, new_password ).
 - get a user by using ( `authenticate(username, old_password)` ) and save is in a var
 - if the user is saved in the system and is authenticate we should have the user in the var.
 - change the password by using ( `set_password(new_pass)` )
 - and save it!!.
 

```python
@api_view(['POST'])  
def edit_user_password(request):  
    # getting user inputs.  
  my_name = request.query_params.get('user')  
    old_p = request.query_params.get('old_pass')  
    new_p = request.query_params.get('new_pass')  
  
    # authenticate the user and save it in (user).  
  user = authenticate(username=my_name, password=old_p)  
  
    # check if the user is in the system.  
  if user is not None:  
  
        # set a new password and save the user to update it in the system.  
  user.set_password(new_p)  
        user.save()  
  
        return HttpResponse('user updated successfully!!')  
  
    else:  
        return HttpResponse(str('Error!! ==> user=' + request.user.username + ' & pass=' + old_p))
```

