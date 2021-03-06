Now let's see how our ConfigMap Watch-Operator works. It's quite similar to the controller logic that we have seen in the last scenario but adapted to read our custom resource.

Again, the script is a shell script which we will later put into a ConfigMap that is mounted by a container and executed.

The main event loop resembles that of our controller as it uses the watch mode to get a stream of events for a ConfigMap:

`bat -r 11:29 operator.sh`{{execute}}

Please refer to the [Controller](21-controller) scenario for a detailed explanation of that main loop.

In the case that a modified ConfigMap is detected, the function `restart_pods_depending_on_cm` is called with the name of the modified ConfigMap as argument:

`bat -r 31:39 operator.sh`{{execute}}

Here, the function fetches all `ConfigWatcher` resources that refer to the ConfigMap for which the MODIFIED event has been received. This is done via the function `get_config_watcher_for_cm`:

`bat -r 41:50 operator.sh`{{execute}}

For every of those `ConfigMaps` first the label selector is extracted in `extract_label_selector_from_watcher`

`bat -r 52:63 operator.sh`{{execute}}

to get a format suitable for a deletion of the Pods in `delete_pods_with_selector`:

`bat -r 65:85 operator.sh`{{execute}}

The Pod deletion is similar to the way how the controller deleted Pods.

------------

Now that we understand how the _Operator_ can restart Pods based on `ConfigWatcher` lets now see in the next step how we can deploy that script.
