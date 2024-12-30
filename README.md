# ibmdb2whl


### **Solution for pip Installation Error of `ibm_db` Library**

#### **Error Description**

While attempting to install and use the `ibm_db` library (e.g., `ibm-db==3.2.4`) on macOS ARM64:

```
(base) woo@Mac ~ % conda activate py311
(py311) woo@Mac ~ % python
Python 3.11.11 (main, Dec 11 2024, 10:25:04) [Clang 14.0.6 ] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import ibm_db
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: dlopen(/Users/woo/anaconda3/envs/py311/lib/python3.11/site-packages/ibm_db.cpython-311-darwin.so, 0x0002): symbol not found in flat namespace '_SQLAllocHandle'
```

------

### **Prerequisite Setup**

#### **1. Install Necessary Tools**

1. Install **Xcode Command Line Tools**:

   ```
   xcode-select --install
   ```

2. Install `unixodbc` via Homebrew:

   ```
   brew install unixodbc
   ```

#### **2. Download IBM Db2 CLI Driver**

Download the appropriate CLI driver for macOS ARM64:

- Link to the CLI driver: [IBM Db2 CLI Driver](https://public.dhe.ibm.com/ibmdl/export/pub/software/data/db2/drivers/odbc_cli/)

- Direct download link for macOS ARM64:

  ```
  https://public.dhe.ibm.com/ibmdl/export/pub/software/data/db2/drivers/odbc_cli/v12.1.0/macarm64_odbc_cli.tar.gz
  ```

------

### **Installation Steps**

#### **3. Extract and Configure Environment Variables**

Extract the downloaded `clidriver` package to a directory (e.g., `/opt/ibm/clidriver`) and configure the necessary environment variables.

Add the following lines to your `~/.zshrc` or `~/.bashrc` file:

```
export IBM_DB_HOME=/opt/ibm/clidriver
export PATH=$IBM_DB_HOME/bin:$IBM_DB_HOME/lib:$PATH
export DYLD_LIBRARY_PATH=$IBM_DB_HOME/lib:$DYLD_LIBRARY_PATH

```

Apply the changes:

```
source ~/.zshrc
```

#### **4. Verify Installation**

1. Ensure the dynamic library path is correctly set:

   ```
   echo $DYLD_LIBRARY_PATH
   ```

2. Validate the CLI driver installation:

   ```
   /opt/ibm/clidriver/bin/db2cli validate
   ```

#### **5. Add Library Path to System Scope**

1. Link the `clidriver` libraries to a system-wide location:

   ```
   sudo mkdir -p /usr/local/lib
   sudo ln -s /opt/ibm/clidriver/lib/* /usr/local/lib/
   ```

2. Remove macOS quarantine restrictions on the directory:

   ```
   sudo xattr -r -d com.apple.quarantine /opt/ibm/clidriver
   ```

------

### **Building `ibm_db` from Source**

1. Clone the `ibm_db` source repository:

   ```
   git clone git@github.com:ibmdb/python-ibmdb.git
   ```

2. Navigate to the desired version directory and build the package:

   ```
   cd python-ibmdb
   python setup.py bdist_wheel
   ```

3. Install the generated `.whl` file from the `dist/` directory:

   ```
   pip install dist/ibm_db-3.2.4-cp311-cp311-macosx_11_0_arm64.whl
   ```

4. Confirm successful installation:

   ```
   (py311) woo@Mac python-ibmdb % python
   Python 3.11.11 (main, Dec 11 2024, 10:25:04) [Clang 14.0.6 ] on darwin
   Type "help", "copyright", "credits" or "license" for more information.
   >>> import ibm_db
   >>>
   ```



## END