PADrendComplete
===============

Super build of PADrend

Building PADrend
----------------

See [Installation Guide](https://padrend.github.io/Tutorials/installation_guide) for an in-depth build guide.

* Clone the Git repository with its submodules:

		git clone --recursive git@github.com:PADrend/PADrendComplete.git PADrend

* Change into your new directory:

		cd PADrend

* Optionally, install the following packages
		
		sudo apt install libcurl4-openssl-dev libxml2-dev

* Build PADrend (in this example by using Ninja):

		mkdir build
		cd build
		cmake -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo ..
		ninja
		cd ..

* Run PADrend:

		build/PADrend
