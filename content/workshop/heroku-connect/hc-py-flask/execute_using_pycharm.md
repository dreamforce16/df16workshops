## Executing using PyCharm

* Install the [Pycharm](https://www.jetbrains.com/pycharm/download/). And add your directory as a project to PyCharm.

  <img src="../images/pycharm1.png" width="90%" height="80%">

* Set the Environment variable as `DATABASE_URL=postgres://<user_name>:<password>@<ipaddress>.compute-1.amazonaws.com:5432/<database_name>` by following the path File > Default Settings
  
  <img src="../images/pycharm3.png" width="90%" height="80%">
  
* Click on `app.py` and run the app.

  <img src="../images/pycharm2.png" width="90%" height="80%">

  Open the following URL `http://localhost:5000/contacts` you should be able see the contacts.