# QIT Template Bundle Creation Tool

This tool provides a way to create bundle definitions for integration into the
QIT diffusion MRI analysis pipeline.  There are many bundles provided
automatically, but you may have one in mind that is not available.  This will
help you create all of the necessary files for adding any bundle you like.

The requirements are providing two masks that are defined in the QIT atlas
(`$QIT_DIR/share/data/tone.nii.gz`, assuming `QIT_DIR` is where you have
installed QIT).  These masks should localize the starting and ending regions of
the bundle, and the associated nifti files should be placed in the `data/masks`
directory.  There are many masks inside that directory, so you may be able to
use an existing one as well.

Let's work through an example, creating a simple model of the cortico-spinal
tract, which connects the precentral gyrus and brainstem.  You can find two
masks for these regions:

* `data/masks/brainstem.nii.gz`
* `data/masks/lh_precentral.nii.gz`

From this tool's directory, you can start by running the following command:

```
bash bin/make-bundle --left brainstem --right lh_precentral --output bundles/example
```

The left and right arguments correspond to the regions connected by the bundle
(not anatomical left and right!).  The output is where the bundle definition
will be saved.  In our case, we provide `brainstem` and `lh_precentral`, which
correspond to the files described above.  If you added your own masks, you can
reference them here.  You can also specify many tractography parameters,
related to seeds, turning threshold, etc.  Check out the script to see what's
available.

After running that command, you can find a "guide" build that is a first guess
at the bundle geometry.  This will likely have many false positives and
outliers, so next, you will need to open the bundle and remove them.  Check out
the QIT tutorials to see how to edit bundles (either with the mouse, spheres,
boxes, or masks):

```
qitview --curves bundles/example/guide.vtk.gz
```

If the bundle looks incomplete, you can try running the `make-bundle` script
again with other options for tracking parameters, e.g. turning angle. Once you
are happy with the guide bundle, you can create the final bundle definition
with the following command:

```
bash bin/make-bundle --left brainstem --right lh_precentral --output bundles/example --final
```

This should be the same command as before, but with `--final` added.  You can
then see the results in qitview with something like this:

``` 
qitview --volume ../data/tone.nii.gz bundles/example/tom.nii.gz --curves bundles/example/curves.vtk.gz bundles/example/proto.vtk.gz  
```

Next, you can install this in your QIT installation by copying that directory
to `$QIT_DIR/share/data/tract/bundles` and adding the bundle name to
`$QIT_DIR/share/data/tract/bundles.txt`.  Now when you run the `qitdiff`
pipeline, this bundle should be reconstucted you subjects.
