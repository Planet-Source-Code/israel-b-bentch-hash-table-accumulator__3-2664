<div align="center">

## Hash Table Accumulator


</div>

### Description

When you ::Store() keys + values to this class, it first checks to see if the key exists, if so, the value that exists is incremented by the inbound value, otherwise a new key and association is created. ** The class uses mfc's hash table classes behind the scenes. Execution is very fast.
 
### More Info
 
const char* sKey

const int* pnData

returns data based on the requested key


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Israel B\. Bentch](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/israel-b-bentch.md)
**Level**          |Advanced
**User Rating**    |5.0 (10 globes from 2 users)
**Compatibility**  |Microsoft Visual C\+\+
**Category**       |[Classes/ Frameworks/ OOP](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/classes-frameworks-oop__3-31.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/israel-b-bentch-hash-table-accumulator__3-2664/archive/master.zip)





### Source Code

```
// **********************************************************************************
// HashAccum.h
//
// Author: Israel B. Bentch
// Use the code however you want. I don't care.
//
// OPERATION: CHashAccum::Store( const char* sKey, const int* pnData )
//				If sKey exists already, then whatever value
//				is existing in the hash table, is incremented
//				by *pnData (the data pointed to, not by the pointer itself)
//
//			  If sKey does not exist, then a new place is created for it
//				in the hash.
//
//				CHashAccum::GetData( const char* sKey, int* pnOutData )
//				Retrieves data based on the key that you pass in.
//				returns non zero if key was found
//
//				CHashAccum::DumpToFile( const char* sfilename )
//				Dumps the entire hash (keys+values) to the specified filename
// EXAMPLE:
//				CHashAccum ha;
//				int i = 10;
//				int out;
//				ha.Store( "key", &i );
//				ha.GetData( "key", &out );
//				printf("this is the retrieved value:%d\n", out );
//				ha.DumpToFile( "dump.txt" );
//
//				happy hashing
//
class CHashAccum
{
public:
	CHashAccum()
	{
	}
	~CHashAccum()
	{
		// if the hash is not empty
		// dealloc all data held by the
		// pointers in it
		if ( !m_Hash.IsEmpty() )
		{
			POSITION pos = m_Hash.GetStartPosition();
			while ( pos != NULL )
			{
				CString key;
				void* pnValue;
				m_Hash.GetNextAssoc(pos, key, pnValue );
				if ( pnValue != NULL )
					delete pnValue;
			}
		}
	} // end DTOR
	//-------------------------------------------------------------
	// dumps the entire hash table to a file
	// keys + data
	void DumpToFile( const char* fname )
	{
		POSITION pos = m_Hash.GetStartPosition();
		CString sOut;
		CStdioFile file;
		file.Open( fname, CFile::modeCreate | CFile::modeWrite );
		while ( pos != NULL )
		{
			CString key;
			void* pnValue;
			m_Hash.GetNextAssoc(pos, key, pnValue );
			sOut.Format("key = %s \t\t value = %d \n",
				key, *(int*)pnValue );
			file.WriteString( sOut );
		}
		file.Close();
	}
	// ------------------------------------------------------------------------
	// IN : const char* sKey	:	the string represents the key
	//		const int* pnData	:	the data to be stored with the key
	// OUT: outputs to the internal hash table
	//
	// OPERATION: If sKey exists already, then whatever value
	//				is existing in the hash table, is incremented
	//				by *pnData (the data pointed to, not by the pointer itself)
	//
	//			  If sKey does not exist, then a new place is created for it
	//				in the hash.
	//
	//				happy hashing
	void Store( const char* sKey, const int* pnData )
	{
		void* pnrData;
		// does the key already exist?
		// 0 if NOT FOUND, nonzero if found
		if ( 0 != m_Hash.Lookup( sKey, pnrData ) )
		{
			// the key exists, so add the value
			// to the existing
			*(int*)pnrData += *(int*)pnData;
			// we're done, so NULL out the pointer
			pnrData = NULL;
		}
		else
		{
			// the key is new, put it in untouched
			int* pnNewData = new int;
			// store the incoming value to our newly created
			// memory
			*pnNewData = *pnData;
			m_Hash.SetAt( sKey, (void*)pnNewData );
			// don't delete the data, we need it to stay
			// in the HASH
		}
	}
	//-------------------------------------------------------------------------
	// this is not a destructive get, it's more like a peek
	// it returns the value, but leaves the key + value in the map
	//
	// RETURNS: 0 IF NOT FOUND, 1 if found
	int GetData( const char* sKey, int* pnOutData )
	{
		void* pnrData;
		if ( 0 != m_Hash.Lookup( sKey, pnrData ) )
		{
			*pnOutData = *(int*)pnrData;
			return 0;
		}
		else
			return 1;
	}
protected:
	CMapStringToPtr	m_Hash;
};
```

