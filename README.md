# Example notebook

- Using small file: [visualize_demo_output.ipynb](./visualize_demo_output.ipynb)
- Using big file on Odyssey: [visualize_large_output.ipynb](./visualize_large_output.ipynb)

# Environment

Install Miniconda, and then:

    conda env create -n ac290 -f ./environment.yaml
    conda activate ac290
    which ipython jupyter

# Note on using Jupyter on Odyssey

## Jupyter on login node

    ssh your-user-name@login.rc.fas.harvard.edu -L 8889:localhost:8889
    conda activate ac290
    jupyter notebook --NotebookApp.token='' --no-browser --port=8889

Visit `localhost:8889` in browser.

## Jupyter on compute node (with sbatch)

Make a script like:

    #!/bin/bash
    #SBATCH -p test
    #SBATCH -N 1
    #SBATCH -n 6
    #SBATCH -t 1:00:00
    #SBATCH --mem-per-cpu 3000
    #SBATCH --job-name notebook
    #SBATCH --output jupyter-address-%J.log

    ## get tunneling info
    jupyter_port=8889  # other number also works
    jupyter_ip=$(hostname -i)

    ## print tunneling instructions to jupyter-log-{jobid}.txt
    echo -e "
        Copy/Paste this in your local terminal to ssh tunnel with remote
        -----------------------------------------------------------------
        ssh your-user-name@login.rc.fas.harvard.edu -N -L ${jupyter_port}:${jupyter_ip}:${jupyter_port}
        -----------------------------------------------------------------

        Then open a browser on your local machine to the following address
        ------------------------------------------------------------------
        localhost:${jupyter_port}  (prefix w/ https:// if using password)
        ------------------------------------------------------------------
        "

    ## launch jupyter
    conda activate ac290
    jupyter-notebook --NotebookApp.token='' --no-browser --port=${jupyter_port} --ip=${jupyter_ip}

Follow instruction in the generated log file.

## Jupyter on compute node (with srun; doesn't work)

Ref: https://informatics.fas.harvard.edu/port-forwarding-on-odyssey-nodes.html

In principle it should work, but I keep getting `srun: Force Terminated job` after launching notebook.
