Dask interface to Azure-Datalake Gen1 and Gen2 Storage Quickstart
-----------------------------------------------------------------


This package is on PyPi and can be installed using:

`pip install adlfs`

The `adl://` and `abfs://` protocols are included in fsspec's known_implementations registry 
in fsspec > 0.6.1, otherwise users must explicitly inform fsspec about the supported adlfs protocols.

To use the Gen1 filesystem:

```python
import dask.dataframe as dd
from pkg_resources import parse_version
import fsspec
if parse_version(fsspec.__version__) < parse_version('0.6.2'):
    from fsspec.registry import known_implementations
    known_implementations['adl'] = {'class': 'adlfs.AzureDatalakeFileSystem'}

STORAGE_OPTIONS={'tenant_id': TENANT_ID, 'client_id': CLIENT_ID, 'client_secret': CLIENT_SECRET}

dd.read_csv('adl://{STORE_NAME}/{FOLDER}/*.csv', storage_options=STORAGE_OPTIONS)
```

To use the Gen2 filesystem:

```python
import dask.dataframe as dd
from pkg_resources import parse_version
import fsspec
if parse_version(fsspec.__version__) < parse_version('0.6.2'):
    from fsspec.registry import known_implementations
    known_implementations['abfs'] = {'class': 'adlfs.AzureBlobFileSystem'}

STORAGE_OPTIONS={'account_name': ACCOUNT_NAME, 'account_key': ACCOUNT_KEY}

ddf = dd.read_csv('abfs://{CONTAINER}/{FOLDER}/*.csv', storage_options=STORAGE_OPTIONS)
ddf = dd.read_csv('abfs://{CONTAINER}/folder.parquet', storage_options=STORAGE_OPTIONS)
```

Details
-------
The package includes pythonic filesystem implementations for both 
Azure Datalake Gen1 and Azure Datalake Gen2, that facilitate 
interactions between both Azure Datalake implementations and Dask.  This is done leveraging the 
[intake/filesystem_spec](https://github.com/intake/filesystem_spec/tree/master/fsspec) base class and Azure Python SDKs.

Operations against both Gen1 Datalake currently only work with an Azure ServicePrincipal
with suitable credentials to perform operations on the resources of choice.

Operations against the Gen2 Datalake are implemented by leveraging [multi-protocol access](https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-multi-protocol-access), using the Azure Blob Storage Python SDK.
The AzureBlobFileSystem accepts [all of the BlockBlobService arguments](https://docs.microsoft.com/en-us/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice?view=azure-python-previous).
