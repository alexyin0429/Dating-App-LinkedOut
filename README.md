# LinkedOut

A versatile modern dating application for all your relationship needs! The interface is based on Java Swing.

The Demo video is here: https://youtu.be/Fc_-o80U5nE

**Notes**:
- This app can only accommodate MacOS system!!!
- The Apache Excel POI package and a Python interpreter are required to run this program. The link to the Apache package is under src/"Package required for accessing database".
- Please disregard the code percentages under Languages to the right: the non-Java code originates from the auxiliary packages which are rather large in file size.

## Project outline

## Group members & responsibilities:
Everyone collborated on each other's tasks, making a clear definition of the responsibilities rather difficult. However, in general, the group members had the following responsibilities:
- Yanlin (Tristal) Li: Recommend User Case, Gateway
- Yifei Zhang: Register, login
- Chengyan (Ryan) Shi: Report, report gateway, record report, access report, restrict user
- Chensheng (Michelle) Xu: Search
- Qingyi (Clara) Hu
- Zezhou (Joe) Cui
- Weilin (Alex) Yin: User Manager Use case, User, RegularUser, VipUser

## Project outline:

### Entities

- The central entity is the account. Its childen are Admin and Profile, which in turn has children User, RegularUser, and VipUser.
- Entities that represent creations by a user are Review and Report.

### Use cases / functionality

#### Recommend Use Case

For a dating app, it is important to recommend users with other users that they may be interested in. 
This use case implements the functionality of recommending other users to the given user. 

For example, you enter the main page and pressed the recommend button, the app can automatically 
recommend a group of other users that you may be interested in and has never seen before
based on our data about you and other users. Based on your gender and sexuality information, the app
will only recommend you those with the gender you usually date with. 

There are two forms of recommendation: **"similar"** and **"recommend"**

##### Similar

When user A is interested in finding similar users to user B, he or she can hit "Similar" button. The app
will then call a K-Nearest-Neighbours model. The model will consider the "liking patterns" (which group of 
users "liked" them) for user B, and then find 20 users who are liked by a similar group of users. 
Finally, the app will display those similar users with the desired gender.

##### Recommend

We have two kinds of recommendation algorithms designed for two kinds of users

1. **New users (never "Liked" any other users before):** We automatically recommend a group of popular users 
(mostly seen by others) of the desired gender to them and display the list on the main screen. 

2. **Old users (has "Liked" other users before):** We use Collaborative Filtering based on K-Nearest-Neighbours 
to find out the users they may like. For recommendation to user A, the algorithm will first automatically pick several 
users with the highest ratings from A. If A hasn't reviewed anyone before, the app will randomly pick 
some users A has "liked" as the basis of recommendation. Based on our database, the K-Nearest-Neighbours model 
will pick 10 users with similar "liking patterns" (liked by similar group of users) for each selected basis user, 
filter out the users who have already been seen, and combine to a maximum of 40 users to recommend. After that, the 
app will display the users with the desired gender. 

##### Classes
>- RecommendController
>- RecommendDsGateway
>- RecommendGateway
>- RecommendInputBoundary
>- RecommendInteractor
>- RecommendPresenter
>- RecommendRequestModel
>- RecommendResponseModel

##### The flow

When a user clicks Recommend or Similar button, RecommendController receives a RecommendRequestModel item, which includes the 
username (and the target user username). The RecommendController uses an input boundary item, which is implemented by RecommendInteractor. 
The interactor calls a python script (recommend.py) to do the data analysis and modelling, and the recommend result is 
displayed in a .xls file. The RecommendInteractor then reads the .xls file by calling the RecommendDsModel, which is implemented by 
RecommendGateway, and outputs the result as a RecommendResponseModel item. RecommendPresenter receives the item and transfer
it into a list of Users, which was then passed to the UI. 

#### Review usecase

>1. When two users liked each other, they can write reviews for each other
>2. A review object contains: int rating, String comment, String writer (writer’s name), String receiver (receiver’s name), int id
>3. Create a review: input writer’s name, receiver’s name, comment and rating
>4. Delete a review: input the review’s id
>5. Hide a review (VIP): input the review’s id (to be implemented later)

##### Classes

>- ReviewController
>- ReviewRequestModel
>- ReviewInputBoundary
>- ReviewInteratcor
>- ReviewGateway
>- ReviewGatewayImplementation
>- ReviewOutputBoundary
>- ReviewPresenter
>- ReviewResponseModel

##### How the code works

> #### The add review’s flow goes like this:
> when the ReviewController takes in the input information from the screen, it then
> constructs a ReviewRequestModel and puts all input information into the request model, it then calls ReviewInputBoundary
> to add the review to the system. The ReviewInteratcor which implements ReviewInputBoundary will construct the review
> object, call the ReviewGateway to save the review to the database, and call ReviewOutputBoundary to report the success
> of adding the review, and the ReviewPresenter which implements the ReviewOutputBoundary will execute the implemented
> method and return a ReviewResponseModel.

> #### The delete review’s flow is basically the same:
> It’s just now a request model is not needed since the user only need to
> input the review’s id to delete it.


#### User Use Case

- User inputs the account name of the user and click `Confirm` first, then click `Profile` button to view this user's profile
- Click `Liked` to view all users liked by the current user
- Click `Status` to view the account status, including Vip Status (boolean), Restriction Status (boolean), and Restriction Duration(float)
- Click `Show Review` to view all the reviews the current user received
- Click `Delete Review` to delete specific review by inputting id of the review
- Click `Upgrade` to upgrade a Regular User to VIP user
- Click `Like Me (VIP)` to display all users the current user has liked (Function only available for VIP User)
- Click `Set Invisible` to set the current user to invisible (Function only available for VIP User)

#### Filing and saving / record report use case

Key features:
- A user can file a report against another user by the UI.
- The report is stored in the database.
- The admin is notified.
- The user sees if this was successful by the UI.
Relevant entities: User, Admin, Report

#### Accessing report	use case

Key features:
- An admin enters the ID of the report to access by the UI.
- The report is retrieved from the database.
- The admin then sees the report shown by the UI.
Relevant entities: Admin, Report

#### Restrict user use case

Key features:
- An admin, after viewing the report, can restrict a user by the UI.
- A restriction duration is set for the user (in Unix seconds).
- The admin sees if this was successful by the UI.
- The user can no longer log in for this amount of time.
Relevant entities: User, Admin

#### UserLike use case
>1. A user is able to like another user.
>2. If two users both liked each other, they are matched, and have access to each other's contact information.

##### The registration Use Case
When using an app, the first thing you need to do is to register an account.
For instance, when the user wants to create an account, the user opens the application and make a registration.
To register, the user need to enter the account, password and profile, which is location, gender, age, sexuality, 
hobbies, height, weight, contactInformation, selfDescription.
Then
1.The app will check if there are any duplicate usernames, if there exist a duplicate, then app will alert the user.
2.The app will check if the password and repeat password matches. The User need to enter password same as twice.
3.The app will store the user which means registration successful.

##### Classes
>- UserRegisterDsGateway
>- UserRegisterDsRequestModel
>- UserRegisterInputBoundary
>- UserRegisterInteratcor
>- UserGateway
>- ProfileGateway
>- UserRegisterPresenter
>- UserRegisterRequestModel
>- UserRegisterResponseModel

##### The Login Use Case
After the registration, the user need to log in to use the app.
For instance, when the user wants to log in, the user opens the application.
To log in, the user need to enter the account, password.
Then
1.The app will check if the account does not exist, if the account does not exist, then app will alert the user.
2.The app will check if the account and password matches. 
3.The app will log in the user.

##### Classes
>- UserLoginDsGateway
>- UserLoginRequestModel
>- UserLoginInputBoundary
>- UserLoginInteratcor
>- UserGateway
>- UserLoginPresenter
>- UserLoginResponseModel

##### The flow
When User A clicked a like button somewhere on a screen, it triggered the action perform method inside the screen class, and the action perform method is going to call the controller and pass in necessary data. Then the controller bunddle the input data into an instance of class InputData, and tell the interactor to do its job through an interface meanwhile passing in the input data. After the interactor has done its work, it will first save the user A’s action into database through UserActDsGateway interface(implemented by LikeGateway), then it asks the presenter to return a view, meanwhile the controller detected the result, then the view module detected the change in controller, and updates the screen, so user gets alerted.

### Design patterns

We have implemented the Factory design pattern for the User entity.

#### Gateway Structure

![](images/Gateway.png)

#### Recommend Use Case
Here is the UML for Recommend Use Case

![](images/recommendUseCase.png)

### Testing:

Here is the coverage report screenshot. You can see the full report in the TestCoverage folder (TestCoverage/index.html)
![](images/coverage.png)

### Future Improvements

1. We believe that we can extend our project by improving the UI, which may be chief among our focus 
(and perhaps the most straightforward) given its importance in the accessibility of our program.
2. We can use different database system, like psql database accessed by Apache Spark. 
