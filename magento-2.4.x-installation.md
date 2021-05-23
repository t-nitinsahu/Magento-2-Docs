1. Extensetion install in php.ini 
		
		In the php.ini file, find these rows and remove ; before each row
		
		;extension=intl
		;extension=soap
		;extension=sockets
		;extension=sodium
		;extension=xsl
		memory_limit=-1

2. Install Elasticsearch
	i. 		Download Elasticsearch 7.6.0: https://www.elastic.co/downloads/past-releases/elasticsearch-7-6-0
	ii. 	Go to .\bin\elasticsearch.bat Run as adminnstration 
	iii. 	Open in browser http://localhost:9200
	

3. Download Composer 1.x
	if you have installed composer 2  then downgrade to an earlier 1.x version using composer self-update --1 in order to ensure the best compatibility



4. Install Magento 2.4.x
	1. cd xampp\htdocs
	2. composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition=2.4.2 <install-directory-name>
	3. Find validateURLScheme function in \vendor\magento\framework\Image\Adapter\Gd2.php and

		replace it with:
		
		private function validateURLScheme(string $filename) : bool
		   {
		       $allowed_schemes = ['ftp', 'ftps', 'http', 'https'];
		       $url = parse_url($filename);
		       if ($url && isset($url['scheme']) && !in_array($url['scheme'], $allowed_schemes) && !file_exists($filename)) {
		           return false;
		       }

		       return true;
		 }

	4. Run Cmd

		php bin/magento setup:install --base-url=http://localhost/<install-directory-name>/ --db-host=localhost --db-name=yourdbname --db-user=yourdbuser --db-password=yourdbpassword --admin-firstname=admin --admin-lastname=admin --admin-email=admin@admin.com --admin-user=admin --admin-password=admin123 --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1 --backend-frontname=admin --search-engine=elasticsearch7 --elasticsearch-host=localhost --elasticsearch-port=9200



		OUTPUT :

		Post installation file permissions check…
		 For security, remove write permissions from these directories: 'C:/xampp/htdocs/magento24/app/etc'
		 [Progress: 1270 / 1270]
		 [SUCCESS]: Magento installation complete.
		 [SUCCESS]: Admin Panel URI: /admin
		Nothing to import.


	5. In vendor\magento\framework\View\Element\Template\File\Validator.php, replace line 138 with:

			$realPath = str_replace('\\', '/',$this->fileDriver->getRealPath($path));


	6.  Run these commands 1 by 1

			php bin/magento indexer:reindex
			php bin/magento setup:upgrade
			php bin/magento setup:static-content:deploy -f
			php bin/magento cache:flush


	7.  Then

		 Step 1: Copy the index.php and .htaccess files from /pub/ to your root folder.
		 Step 2: Find the below line in the index.php (in the root folder):
		 	
		 	require __DIR__ . '/../app/bootstrap.php'; 

		 	and replace it with

		 	require __DIR__ . '/app/bootstrap.php';


	8.  Go To your database 

		and Run

		INSERT INTO `core_config_data` (`config_id`, `scope`, `scope_id`, `path`, `value`, `updated_at`) VALUES (NULL, 'default', '0', 'web/secure/base_static_url', 'http://localhost/<install-directory-name>/pub/static/', current_timestamp());
		
		INSERT INTO `core_config_data` (`config_id`, `scope`, `scope_id`, `path`, `value`, `updated_at`) VALUES (NULL, 'default', '0', 'web/unsecure/base_static_url', 'http://localhost/<install-directory-name>/pub/static/', current_timestamp());
		
		INSERT INTO `core_config_data` (`config_id`, `scope`, `scope_id`, `path`, `value`, `updated_at`) VALUES (NULL, 'default', '0', 'web/secure/base_media_url', 'http://localhost/<install-directory-name>/pub/media/', current_timestamp());
		
		INSERT INTO `core_config_data` (`config_id`, `scope`, `scope_id`, `path`, `value`, `updated_at`) VALUES (NULL, 'default', '0', 'web/unsecure/base_media_url', 'http://localhost/<install-directory-name>/pub/media/', current_timestamp());


	9. php bin/magento cache:flush