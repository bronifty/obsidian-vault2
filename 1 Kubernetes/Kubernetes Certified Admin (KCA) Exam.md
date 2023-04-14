# Kubernetes Certified Admin (KCA) Exam

- [https://blog.devgenius.io/passing-the-2023-certified-kubernetes-administrator-cka-exam-693d8f9bc711](https://blog.devgenius.io/passing-the-2023-certified-kubernetes-administrator-cka-exam-693d8f9bc711)
- linux foundation cert: [https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/](https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/?code=AP6G3GokwZ9Lymso9jey5XYL2AglgWmpjxkBSCM6zraJy&state=NEJIQzZJbGJOQ2htZ3ZwQ1l6eXdGZHFLcHAtNnMxeGRfRU0xWGVhZ2p5Rw%3D%3D)
- course:
    - [https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/)
    - access to KodeKloud

## **Killer.sh exam simulation**

When you register for the exam at the Linux Foundation, you get two free Killer.sh simulator sessions. Each session, you have 36-hour access to a Kubernetes cluster where you have to answer the 24 exam questions.

Killer.sh states that their exam is more challenging than the actual exam. I disagree. They are the same. The difference is that the killer.sh exam got 22 questions, while on the actual exam, there are 17.

## Technique

*Create a pod called MyPod in namespace fire using the nginx:alpine image. Name the container in the pod MyPod-container.*

We can create the pod using the following command.

```
k -n fire run MyPod --image=nginx:alpine
```

Now, this would work, but one thing that we can’t specify is the name of the container in the pod.

The solution is to create a yaml file using the imperative command. See the example below. Adding the `--dry-run=client` argument, kubectl does not run the command against the Kubernetes cluster but validates it. The `-o yaml` statement means we want to export the command using the yaml syntax.

```
k -n fire run MyPod --image=nginx:alpine --dry-run=client -o yaml > pod.yaml
```

This generates a file pod.yaml as shown below. Do you remember the requirement that the container is named “MyPod-container”? In this file, we can change the name of the container and create the pod using `k apply -f pod.yaml`.

Creating the yaml file using the dry-run argument and changing it

I used this technique with almost all the exam questions. First, generate a yaml file using an imperative command, change the file according to the requirements and create the resource using `apply`.

## Commands

export do="--dry-run=client -o yaml"

export now="--grace-period 0 --force"

I showed in the Terminal section that I created two environment variables called `do` and `now`. The first one `do` contains `--dry-run=client -o yaml`. If we combine this with the imperative command for creating a pod, we get the following. This again saves a couple of keystrokes.

```
k -n fire run MyPod --image=nginx:alpine $do > pod.yaml
```

# **Deleting Kubernetes resources**

Sometimes you need to delete a Kubernetes resource before you can update it. Deleting an object can take some time, typically 10 seconds. As you need all the time on the exam, you can speed it up by adding the arguments `--force --grace-period 0` . This will directly delete the object.

Combining this with the new environment variable `now` we discussed earlier, we get the following command which saves another couple of keystrokes.

```
k -n fire delete pod MyPod $now
```

# **Skip difficult questions with a low rating**

Some exam questions are more complex than others. Each question shows a weight (percentage) that indicates how much this question contributes to the exam score.

Try to answer as many questions as possible. This means skipping a question when it takes too much time. For example, when kubectl keeps complaining about syntax errors in your yaml, flag the question and continue with the next question.

You can always reevaluate if you have time left at the end of the exam.