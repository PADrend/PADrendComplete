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

* Build the third party libraries (in this example using Makefiles):

		cd ThirdParty
		mkdir build
		cd build
		cmake ..
		make -j8
		cd ../..
		
* or, install the following packages
		
		sudo apt install libcurl4-openssl-dev libglew-dev libopenal-dev libpng-dev libsdl2-dev libsdl2-image-dev libsdl2-net-dev libsqlite3-dev libxml2-dev libzip-dev libfreetype6-dev libarchive-dev zlib1g-dev

* Build PADrend (in this example by using Ninja):

		mkdir build-relwithdebinfo
		cd build-relwithdebinfo
		cmake -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo ..
		ninja
		cd ..

* Run PADrend:

		build-relwithdebinfo/PADrend/PADrend
