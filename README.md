# RestApiExpressServerTemplateExplained

Simple file to explain more or less how does an express api works nd the rest of the code in general also to myself to use it as a template to set up logplease, or express or sequelize. 


Logger set up
Import logplease from logplease module installed with the Node Package Manager (NPM)
```const Logger = require('logplease');```
Set up the showTimestamp property to false 
```const logSetUp = {showTimestamp: false};```
Assign the called imported logplease function with the configutation variable to another variable
```const log = Logger.create("",logSetUp);```
Express set up
Import Express from the Express module package installed with NPM
```const express = require('express');```
Assigne the called imported Express function to a variable
```const app = express();```
bodyParser set up
Import body-parser, you already know how and from where...
```const bodyParser = require('body-parser');```
Sequelize set up
Same, import sequelize...
```const sequelize = require('sequelize');```
Port defined, assign whatever port you want to run the server on.
```const port = 4200;```

Parse received information from the body of client request using predefined body-parser json function that returns middleware that only parses json
```app.use(bodyParser.json());```
Same than last one but returns middleware that only parses urlencoded bodies instead of json. 
```app.use(bodyParser.urlencoded({extended:false}));```

Assigns creating a new database with sequelize to a variable
```const sequelizeDb = new sequelize('yourDBName', 'yourDBUser', 'yourDBPassword',{```
Specify type of relational database for sequelize
    ```dialect:"mysql",
});```

Creates a new model using the last variable to target the database and the predefined method by sequelize .define() witch creates a new table following the given parameters, the first parameter is a string referencing the model and the second one is an object with the table fields as properties and specifying the type of the field value for sequelize. Last parameter is in this case to specify the name of the table but it's also for other options.
```const dbTable = sequelizeDb.define('dbTable', {```
Describing table fields and value types for sequelize as object properties and values
   ```title: sequelize.STRING,```
    ```desc: sequelize.TEXT```
```},{tableName:"Database_Name"});```

Sincronize the model with the database. If no table exists matching the model it will create one.
```dbTable.sync();```

This .authenticate() is a predefined function by sequelize that checks the database connection. And return one thing or another in base to that connection.
```sequelizeDb.authenticate().then(()=>{```
   ``` log.info('Database connected succesfully...')```
```}).catch((err)=>log.error("You, developer, here is the bug... "+ err))```

Here we start the CRUD this is the endpoint to create something and add it to the database, in this case a blogpost with title and description but it can be modified.
```app.post("/", async (req, res) => {```
Get the values from th body post request
    ```const title = req.body.title;
    const desc = req.body.desc;```
Build a new model instance following the previously defined model
    ```const saveBlog = dbTable.build({
      title,
      desc,
    });```
Save the data into that instance
 ```  await saveBlog.save();```
Feedback
 ```   log.info("Data saved succesfully!")```
 ```   res.send("Data saved");```
 ``` });```

Endpoint to read from the database
 ``` app.get("/", async (req, res) => {```
Get all table information and save it into a variable
 ```  const alldata = await dbTable.findAll();```
Log that variable or send it to the frontend
 ```  log.debug(alldata);```
res.json(alldata);
 ```   res.sendFile("https://github.com/gabriel19971029/RestApiExpressServerTemplateExplained.git/index.html");```
 ``` });```

Endpoint to update a specific element from the database passing the id
```  app.put("/:id", (req, res) => {```
Save new data to a variable
   ``` const data = req.body.data;```
Sequelize predefined function to update the info, first parameter reassigns the old properties or field to the new values. The second parameter can specify several things, in this case specifies what row/s or element/s has/have to be updated.
   ``` dbTable.update(```
     ``` {```
       ``` desc: data,```
  ```   },```
     ``` {```
    ```    where: {```
   ```       id: req.params.id,```
      ```  },```
   ```   }```
  ```  );```
As a response redirect to main page
```    res.redirect("/");```
 ``` });```

Endpoint to delete a specific element from the database passing the id
```  app.delete("/:id", (req, res) => {```
Predefined by Sequelize .destroy() method to delete a specific element from the database table, in this case getting only one parameter specifying the element by giving the id 
   ``` dbTable.destroy({```
   ```   where: {```
    ```    id: req.params.id,```
    ```  },```
    ```});```
   ``` res.redirect("/");```
 ``` });```

Express predefined .listen() function starts the server and builds it with the predefined express template.
```app.listen(port, ()=>{```
Give feedback for humans.
   ``` log.info(`Server up and running on port ${port}...`);
    log.warn(`Remember, your server is running on port ${port}...`);
})```
