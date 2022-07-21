Kill Process Using Port
===================================

Open ~/.bashrc or ~/.zshrc (if you use zsh) and add the following code at the end of file.

.. code-block:: bash

    function killp(){
	PID=$(netstat -nlp |grep $1| grep -oP '\d+/node'| tr -d '\node/n');
	if [ -z $PID ] 
	then
		echo No process is using port $1;
	else
		echo Killing process $PID which was using port $1;
		kill -9 $PID
	fi
    }
    alias killp="killp"

Now, Open a new terminal or run in the same terminal,

.. code-block:: bash
    
    $ source ~/.zshrc


---------------------
Usage
---------------------

Example:

To kill process using port 3001.

.. code-block:: bash

    $ killp 3001