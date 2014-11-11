## *** ERROR IN <> -- Ill-formed expression
These errors will generally be caused by an incorrect installation of the __.gambcini__ file. If that's the case, please look at the whole output for any error that looks related to this and open an Issue in Github. Meanwhile, you can fix it creating .gambcini manually containing this:

~~~
(let ((spheres-file "~~spheres/spheres#.scm"))
  (if (file-exists? spheres-file)
      (eval `(include ,spheres-file))
      (println "spheres#.scm missing -- Did you install Core Sphere?")))
~~~



