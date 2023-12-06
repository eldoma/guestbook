<<<<<<< HEAD
# coding-project-template
=======
# guestbook
# Made with Docker, staged by Kubernetes on OpenShift
>>>>>>> - Clone the repo [ ! -d 'guestbook' ] && git clone [ https://github.com/source-repo]

-cd guestbook

-cd v1/guestbook

-export MY_NAMESPACE=sn-labs-$USERNAME # Export namespace as an environment variable so that it can be used in subsequent commands. Do this prior running every changes in html or deployment file.

-docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1 #Building guestbook

-docker push us.icr.io/$MY_NAMESPACE/guestbook:v1 # Push the image to IBM Cloud Container Registry

-ibmcloud cr images # Verify that the image was pushed successfully.

-Replace <your sn labs namespace> with your SN labs namespace. To check your SN labs namespace, please run the command ibmcloud cr namespaces

-kubectl apply -f deployment.yml # Apply Deployment. 

-kubectl port-forward deployment.apps/guestbook 3000:3000 # Launching our application on port 3000

-Try out the guestbook by putting in a few entries.

-kubectl autoscale deployment guestbook --cpu-percent=5 --min=1 --max=10 #Autoscale the Guestbook Deployment

-kubectl get hpa guestbook # check the current status of the newly-made HorizontalPodAutoscale (HPA)

-kubectl run -i --tty load-generator --rm --image=busybox:1.36.0 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- <your app URL>; done" 
## Open another new terminal and enter the above command to generate load on the app to observe the autoscaling (Please ensure your port-forward command is running. In case you have stopped your application, please run the port-forward command to re-run the application at port 3000.) Use the Port URL / same copied URL which of the previous task.


-kubectl get hpa guestbook --watch # observe the replicas increase in accordance with the autoscaling

-Please close the other terminals where load generator (optional to close) and port-forward commands are running.
-Open new terminal, Run the below command to observe the details of the horizontal pod autoscaler:
kubectl get hpa guestbook

Perform Rolling Updates and Rollbacks on the Guestbook application:

-update the title and header in index.html to any other suitable title
-Run the below command to build and push your updated app image:
docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1 && docker push us.icr.io/$MY_NAMESPACE/guestbook:v1

IF FAIL, run this first:
-export MY_NAMESPACE=sn-labs-$USERNAME # Export namespace as an environment variable so that it can be used in subsequent commands. Do this prior running every changes in html or deployment file.

- Update the values of the CPU in the deployment.yml to cpu: 5m and cpu: 2m as below:
kubectl apply -f deployment.yml


-Open a new terminal and run the port-forward command again to start the app:
kubectl port-forward deployment.apps/guestbook 3000:3000  

-Launch application on Port 3000

- App will be updated


Please stop the application before running the next steps.

-Run the below command to see the history of deployment rollouts:
kubectl rollout history deployment/guestbook

-Run the below command to see the details of Revision of the deployment rollout:
kubectl rollout history deployments guestbook --revision=2

- Run the below command to get the replica sets and observe the deployment which is being used now:
kubectl get rs


-Run the below command to undo the deploymnent and set it to Revision 1:
kubectl rollout undo deployment/guestbook --to-revision=1


-Run the below command to get the replica sets after the Rollout has been undone:
kubectl get rs


## IBM Cloud Container Registry scans images for common vulnerabilities and exposures to ensure that images are secure. But OpenShift also provides an internal registry. There is less latency when pulling images for deployments. What if we could use both—use IBM Cloud Container Registry to scan our images and then automatically import those images to the internal registry for lower latency?

-Create an image stream that points to your image in IBM Cloud Container Registry:
oc tag us.icr.io/$MY_NAMESPACE/guestbook:v1 guestbook:v1 --reference-policy=local --scheduled

-on Cloud: Click on Open OpenShift console which will open the Open Shift Web console in a new window.

-From the Developer perspective, click the +Add button to add a new application to this project.

-Click the Container Image option so that we can deploy the application using an image in the internal registry.

-Under Image, switch to “Image stream tag from internal registry“.

-Select your project, and the image stream and tag you just created (guestbook and v1, respectively). You should have only have one option for each of these fields anyway since you only have access to a single project and you only created one image stream and one image stream tag.

-Keep all the default values and hit Create at the bottom. This will create the application and take you to the Topology view.

-From the Topology view, click the guestbook Deployment. This should take you to the Resources tab for this Deployment, where you can see the Pod that is running the application as well as the Service and Route that expose it.
Kindly wait as the deployments in the Topology view may take time to get running.
Kindly do not delete the opensh.console deployment in the Topography view as this is essential for the OpenShift console to function properly.

-Click the Route location (the link) to view the guestbook in action.
Note: Please wait for status of the pod to change to ‘Running’ before launching the app.

-Try out the guestbook by putting in a few entries. You should see them appear above the input box after you hit Submit.

- Let’s update the guestbook and see how OpenShift’s image streams can help us update our apps with ease.
Use the Explorer to edit index.html in the public directory. The path to this file is guestbook/v1/guestbook/public/index.html.
Let’s edit the title to be more specific. On line number 12, that says <h1>Guestbook - v1</h1>, change it to include your name. Something like <h1>Alex's Guestbook - v1</h1>. Make sure to save the file when you’re done.

-Build and push the app again using the same tag. This will overwrite the previous image.
docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1 && docker push us.icr.io/$MY_NAMESPACE/guestbook:v1

IF FAIL, run this first:
-export MY_NAMESPACE=sn-labs-$USERNAME # Export namespace as an environment variable so that it can be used in subsequent commands. Do this prior running every changes in html or deployment file.

- Recall the --schedule option we specified when we imported our image into the OpenShift internal registry. As a result, OpenShift will regularly import new images pushed to the specified tag. Since we pushed our newly built image to the same tag, OpenShift will import the updated image within about 15 minutes. If you don’t want to wait for OpenShift to automatically import the image, run the following command:
oc import-image guestbook:v1 --from=us.icr.io/$MY_NAMESPACE/guestbook:v1 --confirm

-Switch to the Administrator perspective so that you can view image streams.

-Click the guestbook image stream.

- Click the History menu. If you only see one entry listed here, it means OpenShift hasn’t imported your new image yet. Wait a few minutes and refresh the page. Eventually you should see a second entry, indicating that a new version of this image stream tag has been imported. This can take some time as the default frequency for importing is 15 minutes

-Return to the Developer perspective.

-View the guestbook in the browser again. If you still have the tab open, go there. If not, click the Route again from the guestbook Deployment. You should see your new title on this page! OpenShift imported the new version of our image, and since the Deployment points to the image stream, it began running this new version as well.
