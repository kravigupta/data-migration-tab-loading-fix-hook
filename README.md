data-migration-tab-loading-fix-hook
===================================

This hook fixes data migration tab loading issue. This is a JSP Hook for Liferay Portal 6.1.20EE.

If you have many files in document library of Liferay portal, this problem will occur. Data migration tab will not get loaded. Each uploaded/updated file creates entries to DLFileEntry and DLFileVersion tables in database. 

When we load data migration tab in Control Panel > Server Administration > Data Migration, Liferay tries to get all the ConvertProcess types which can be used to migrate data. One of which is com.liferay.portal.convert.ConvertDocumentLibraryExtraSettings for which the query below runs. This query takes a lot of time to complete and depends on number of rows in both tables. This query may take almost 10 minutes for just 10000+ documents in your document library.

SELECT 
COUNT(DISTINCT DLFileEntry.fileEntryId) 
AS COUNT_VALUE 
FROM 
DLFileEntry, DLFileVersion
WHERE 
(DLFileEntry.extraSettings not like '') 
OR 
((DLFileVersion.fileEntryId = DLFileEntry.fileEntryId) 
AND (DLFileVersion.extraSettings not like '') )

Usually the result is zero and the method call convertProcess.isEnabled() returns false. And if it is false, ConvertDocumentLibraryExtraSettings is not going to appear in the dropdown which lists all available conversion process. In this case, it should be safe to use this hook. 

We do not have opportunity to always change the db and optimize indexes and queries. This query is in Liferay core code and we can not modify this. The good thing is that we do not always load ConvertDocumentLibraryExtraSettings to migrate data and if you can skip that, you are free to use this hook.

Steps to deploy and use:
========================
This porltet deploys just like other hot deployable plugins of Liferay.<br/>
1. If you have the source code, build it to a war file.<br/>
2. Put the package file in deploy folder of Liferay Portal.<br/>
3. Now navigate to the Control Panel > Server Administration > Data Migration <br/>
4. It should be loaded pretty fast just like other tabs even if your Document library has a large number of documents.<br/>

Links :
=======

1. [Liferay] Data Migration Tab Loading Issue Fix
http://techdc.blogspot.in/2014/11/liferay-data-migration-tab-loading.html