PADrendComplete
===============

Super build of PADrend

Building PADrend
----------------

* Clone the Git repository with its submodules:

		git clone --recursive git@github.com:PADrend/PADrendComplete.git PADrend

* Change into your new directory:

		cd PADrend

* Build the third party libraries (in this example using Makefiles):

		cd ThirdParty
		mkdir build
		cd build
		cmake ..
		make -j8
		cd ../..

* Build PADrend (in this example by using Ninja):

		mkdir build-relwithdebinfo
		cd build-relwithdebinfo
		cmake -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo ..
		ninja
		cd ..

* Run PADrend:

		build-relwithdebinfo/PADrend/PADrend
