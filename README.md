# RestApiExpressServerTemplateExplained

Simple file to explain more or less how does an express api works and the rest of the code in general also to myself to use it as a template to set up logplease, or express or sequelize. 


<br>Logger set up
<br>Import logplease from logplease module installed with the Node Package Manager (NPM) and assign it to a constant variable to use it in your code.
<br>```const Logger = require('logplease');```
<br>Set up the logplease showTimestamp property to false if you don't want to see that consolelogged.
<br>```const logSetUp = {showTimestamp: false};```
<br>Assign the called imported logplease function with the configutation variable to another variable
<br>```const log = Logger.create("",logSetUp);```
<br>
<br>Express set up
<br>Import Express from the Express module package installed with NPM
<br>```const express = require('express');```
<br>Assigne the called imported Express function to a variable
<br>```const app = express();```
<br>
<br>bodyParser set up
<br>Import body-parser, you already know how and from where...
<br>```const bodyParser = require('body-parser');```
<br>
<br>Sequelize set up
<br>Same, import sequelize...
<br>```const sequelize = require('sequelize');```
<br>Port defined, assign whatever port you want to run the server on.
<br>```const port = 4200;```
<br>Parse received information from the body of client request using predefined body-parser json function that returns middleware that only parses json
<br>```app.use(bodyParser.json());```
<br>Same than last one but returns middleware that only parses urlencoded bodies instead of json. 
<br>```app.use(bodyParser.urlencoded({extended:false}));```
<br>Assigns creating a new database with sequelize to a variable
<br>```const sequelizeDb = new sequelize('yourDBName', 'yourDBUser', 'yourDBPassword',{```
<br>Specify type of relational database for sequelize
<br>    ```dialect:"mysql",
});```
<br>
Creates a new model using the last variable to target the database and the predefined method by sequelize .define() witch creates a new table following the given parameters, the first parameter is a string referencing the model and the second one is an object with the table fields as properties and specifying the type of the field value for sequelize. Last parameter is in this case to specify the name of the table but it's also for other options.
<br>```const dbTable = sequelizeDb.define('dbTable', {```
<br>Describing table fields and value types for sequelize as object properties and values
   <br>```title: sequelize.STRING,```
    ```desc: sequelize.TEXT```
```},{tableName:"Database_Name"});```
<br>
Sincronize the model with the database. If no table exists matching the model it will create one.
<br>```dbTable.sync();```
<br>
<br>This .authenticate() is a predefined function by sequelize that checks the database connection. And return one thing or another in base to that connection.
<br>```sequelizeDb.authenticate().then(()=>{```
   ``` log.info('Database connected succesfully...')```
```}).catch((err)=>log.error("You, developer, here is the bug... "+ err))```
<br>
Here we start the CRUD this is the endpoint to create something and add it to the database, in this case a blogpost with title and description but it can be modified.
<br>```app.post("/", async (req, res) => {```
<br>Get the values from the body post request
    <br>```const title = req.body.title;
    const desc = req.body.desc;```
<br>Build a new model instance following the previously defined model
    <br>```const saveBlog = dbTable.build({
      title,
      desc,
    });```<br>
Save the data into that instance<br>
<br> ```  await saveBlog.save();```
<br>Feedback
 <br>```   log.info("Data saved succesfully!")```
 ```   res.send("Data saved");```
 ``` });```
<br>
Endpoint to read from the database
<br> ``` app.get("/", async (req, res) => {```
<br>Get all table information and save it into a variable
<br> ```  const alldata = await dbTable.findAll();```
<br>Log that variable or send it to the frontend
 <br>```  log.debug(alldata);```
<br>res.json(alldata);
 <br>```   res.sendFile("https://gabriel19971029.github.io/RestApiExpressServerTemplateExplained/index.html");```
 ``` });```
<br>
Endpoint to update a specific element from the database passing the id
<br>```  app.put("/:id", (req, res) => {```
<br>Save new data to a variable
   <br>``` const data = req.body.data;```<br>
Sequelize predefined function to update the info, first parameter reassigns the old properties or field to the new values. The second parameter can specify several things, in this case specifies what row/s or element/s has/have to be updated.
   <br>``` dbTable.update(```
     ``` {```
       ``` desc: data,```
  ```   },```
     ``` {```
    ```    where: {```
   ```       id: req.params.id,```
      ```  },```
   ```   }```
  ```  );```
<br>As a response redirect to main page
<br>```    res.redirect("/");```
 ``` });```
<br>
Endpoint to delete a specific element from the database passing the id
<br>```  app.delete("/:id", (req, res) => {```
<br>Predefined by Sequelize .destroy() method to delete a specific element from the database table, in this case getting only one parameter specifying the element by giving the id 
   <br>``` dbTable.destroy({```
   ```   where: {```
    ```    id: req.params.id,```
    ```  },```
    ```});```
   ``` res.redirect("/");```
 ``` });```
<br>
Express predefined .listen() function starts the server and builds it with the predefined express template.
<br>```app.listen(port, ()=>{```
<br>Give feedback for humans.
   <br>``` log.info(`Server up and running on port ${port}...`);
    log.warn(`Remember, your server is running on port ${port}...`);
})```
