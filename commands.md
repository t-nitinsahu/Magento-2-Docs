
1. sudo apt-get install composer

2. composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition /var/www/html/magento2

3. cd /var/www/html/magento2 && find var vendor pub/static pub/media app/etc -type f -exec chmod g+w {} \; && find var vendor pub/static pub/media app/etc -type d -exec chmod g+w {} \; && chmod u+x bin/magento

4. su your_system_user

5. cd /var/www/html/magento2

6. sudo php bin/magento setup:install --base-url=http://127.0.0.1/magento2/ \
--db-host=localhost --db-name=magento2 --db-user=root --db-password=root \
--admin-firstname=Magento --admin-lastname=User --admin-email=user@example.com \
--admin-user=admin --admin-password=admin123 --language=en_US \
--currency=USD --timezone=America/Chicago --use-rewrites=1


7. sudo chmod -R 777 pub var


8. sudo php bin/magento sampledata:deploy

9. sudo php bin/magento cache:clean && sudo php bin/magento setup:upgrade
