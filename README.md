# Serve a Machine Learning Model as a Webservice [Window 10 Home]

Before reading this ReadME, go through [Simple way to deploy machine learning models to cloud](https://towardsdatascience.com/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf) first. As this ReadME is written based on the article, but in a Window 10 flavor.
Serving a simple machine learning model as a webservice using [flask](http://flask.pocoo.org/) and [docker](https://www.docker.com/). Additional tools such as [Postman](https://www.postman.com/), [Putty](https://www.putty.org/), and [WinSCP](https://winscp.net/eng/index.php) have been used.

## Getting Started

1. Use model.py to train a logistic regression model on the [iris dataset](http://archive.ics.uci.edu/ml/datasets/iris) and generate a pickled model file (iris_trained_model.pkl)
2. Use app.py to wrap the inference logic in a flask server to serve the model as a REST webservice:

   - Execute the command `python app.py` to run the flask app.
   - Go to the browser and hit the url `localhost:80` to see the following image. **NOTE**: A permission error may be received at this point. In this case, run the Command Prompt as the **administrator**. Then run `net stop http` to free the port 80 on localhost Windows.

   - Next, test the model prediction by inputting float from **0.0 - 4.0**, once click the button below. You should see the predicted results displayed at the bottom.
   <p align="center">
    <img  src="https://github.com/damien2012eng/deploy-ml-model-windows10/blob/main/images/localPredict.JPG">
   </p>

3. Run `docker build -t app-iris .` to build the docker image using `Dockerfile`. (Pay attention to the period in the docker build command). Run `docker images`, a new image named as "app-iris" should be now available. You can also check images and containers within the docker desktop.
4. Run `docker run -p 80:80 app-iris` to run the docker container that got generated using the `app-iris` docker image.
5. Similar to the previous step, we can test the application via web brouser and Postman.

- Go to the browser and hit the url `localhost:80` to get to the previous html page.

6. Hosting the docker container on an AWS ec2 instance and consuming the web-service.
   If you are new to AWS, Tanuj's [blog](https://towardsdatascience.com/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf) should guide you through how to initialize the EC2 instance. The ReadME would provide additional procedures for Windows 10. Hopefully, it would save you some headaches.

- Download the pem file from the AWS as followed the Tanuj's blog. Open [PuttyGen](https://www.puttygen.com/) to generate a ppk file from the downloaded pem file.
- Make a connection from the local to the AWS EC2. Go to AWS instance, copy the Public IPV4 address to `-Putty -Session -Host Name`. Next, load the ppk file that we just generated to `-Putty -Connection -SSH -Auth -Private Key file` for authentication. Click the Open button in the buttom, a Putty window will pop up. Insert `ec2-user` to enter the command line interface (CLI). if you get lost, follow the steps in the [Connect to AWS EC2 instance via ssh from Windows](https://www.youtube.com/watch?v=f52IOtTqcP8&t=364s)
<p align="center">
 <img  src="https://github.com/damien2012eng/deploy-ml-model-windows10/blob/main/Putty1.JPG">
</p>
- Pass files to the AWS. There are several ways to pass files from you Windows to AWS. What I used here is WinSCP, which provides a good User Interface. Follow the instruction:

[Open EC2 Instance in Windows - Putty , WinSCP | AWS EC2 tutorial for beginners](https://www.youtube.com/watch?v=F2HNtz2Z-00)

to establish the connection. Make sure that you select the pem file to generate a new ppk file, and load the ppk file in the WinSCP.

- Now you should have everything ready. Drag four files into your AWS instance. For details see the attached image.
<p align="center">
 <img  src="https://github.com/damien2012eng/deploy-ml-model-windows10/blob/main/WinSCP3.JPG">
</p>
- Go back to your Putty CLI. Insert ls to double-check if the file are present. Perform the docker installations on the AWS.

```sudo amazon-linux-extras install docker
    sudo yum install docker
    sudo service docker start
    sudo usermod -a -G docker ec2-user
```

Log out of the ec2 instance using the ‘exit’ command and log back in again using the ssh command. Check if docker works by issuing the ‘docker info’ command. Log out again or open another terminal window.

- Next, build and run the docker image using the exact same commands that were used in the local system. Run `docker build -t app-iris .`. Run `docker run -p 80:80 app-iris` to run the docker container.
- Copy your Public IPV4 address to the browser to check if it is working.
  Thank you for reading, and raise issues if you run into any problems.

For details on floating the containerized app on [AWS ec2 instance](https://aws.amazon.com/ec2/), see the [blog](https://medium.com/@tanuj.jain.10/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf).

Reference:
[Simple way to deploy machine learning models to cloud](https://towardsdatascience.com/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf)
[Connect to AWS EC2 instance via ssh from Windows](https://www.youtube.com/watch?v=f52IOtTqcP8&t=364s)
[Open EC2 Instance in Windows - Putty , WinSCP | AWS EC2 tutorial for beginners](https://www.youtube.com/watch?v=F2HNtz2Z-00)
