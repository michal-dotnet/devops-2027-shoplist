                                         | רכיב               | איפה הוא רץ                 | מה הוא עושה, במשפט אחד                                  | אנלוגיה          |
| ------------------ | --------------------------- | ------------------------------------------------------- | ----------------------- |
| API server         | Node ההנהלה (Control Plane) | מקבל את כל הבקשות לניהול Kubernetes ומתווך בין הרכיבים. | פקיד הקבלה              |
| etcd               | Node ההנהלה                 | שומר את כל המידע והמצב של האשכול (Cluster).             | ארכיון / מחסן מידע      |
| scheduler          | Node ההנהלה                 | מחליט על איזה Node ירוץ כל Pod חדש.                     | משבץ עבודה              |
| controller manager | Node ההנהלה                 | מוודא שהמצב בפועל של האשכול תואם למצב הרצוי.            | מנהל שמפקח שהכול מתבצע  |
| kubelet            | בכל Node                    | אחראי לוודא שה-Pods שהוקצו ל-Node שלו רצים כראוי.       | מנהל משמרת              |
| container runtime  | בכל Node                    | אחראי ליצור ולהריץ את ה-Containers בפועל.               | המנוע שמפעיל את המכונות |
| kube-proxy         | בכל Node (בדרך כלל)         | מנהל את חוקי הרשת שמאפשרים תקשורת בין השירותים ל-Pods.  | מרכזנית שמנתבת שיחות    |




1\. מריצים את הפקודה `kubectl scale`, והבקשה מגיעה ל-API Server שמעדכן ב-etcd את מספר ה-Pods הרצוי ל-4. ה-Controller Manager מזהה שחסרים שני Pods ודואג ליצור אותם. ה-Scheduler משבץ אותם ב-Nodes מתאימים, וה-Kubelet מפעיל אותם באמצעות ה-Container Runtime.

2\. כאשר Container קורס, ה-Kubelet מבקש מה-Container Runtime להפעיל אותו מחדש בהתאם ל-Restart Policy. אם מוחקים את ה-Pod כולו, Deployment ידאג ליצור Pod חדש. אם מדובר ב-Pod עצמאי, הוא לא יחזור.

3\. כאשר Node נכבה, ה-Pods שרצו עליו מפסיקים לעבוד. ה-Controller Manager דואג ליצור Pods חלופיים, וה-Scheduler משבץ אותם ב-Nodes אחרים. ב-Docker Desktop, כאשר המחשב נכבה, כל סביבת Kubernetes המקומית נעצרת.

4\. ההגדרה `imagePullPolicy: Never` לא מתאימה לענן, כי התמונה לא בהכרח קיימת שם. צריך להעלות אותה ל-Registry כמו Amazon ECR ולהשתמש ב-`IfNotPresent`. בנוסף, במקום `NodePort` מקובל להשתמש ב-`LoadBalancer` או ב-Ingress כדי לחשוף את האפליקציה.

5\. Pod שנוצר באמצעות `kubectl run` הוא עצמאי, ולכן לאחר מחיקתו הוא לא חוזר. לעומת זאת, Pod ששייך ל-Deployment נוצר מחדש על ידי ה-Controller Manager כדי לשמור על מספר ה-Pods שהוגדר. כדי ש-Pod יחזור אוטומטית, יש ליצור אותו באמצעות Deployment.


