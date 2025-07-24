# Magento 2 Redis session patch

Redis patch for session management optimization

## Compatible with
- Magento 2.4.8
- php-redis-session-abstract 2.1.2

## Installation

To install patches with composer you need to have "cweagans/composer-patches" installed first.
If you don't have it installed you can do it with:
```
composer require cweagans/composer-patches
```
Then, add the patches to your composer.json

    "extra": {
        "magento-force": "override",
        "patches": {
            "magento/framework": {
                "Serialize session": "https://raw.githubusercontent.com/olivertar/m2_redis_patch/refs/heads/main/patches/colinmollenhour/php-redis-session-abstract/2.1.2/set-php-serialize-handler.patch"
            },
            "colinmollenhour/php-redis-session-abstract": {
                "Session lock write only": "https://raw.githubusercontent.com/olivertar/m2_redis_patch/refs/heads/main/patches/colinmollenhour/php-redis-session-abstract/2.1.2/implement-write-lock.patch"
            }
        }
    }

Then run the commands

```
composer install
bin/magento setup:upgrade --keep-generated
```

## What is this patch?
When using Redis as session storage in Magento 2, simultaneous or closely spaced requests to the same session can end up queued due to the locking system that prevents concurrent writes.

This behavior is particularly affecting environments with multiple AJAX calls (such as checkout) or headless frontends, generating unnecessary delays even when most requests only read the session and do not modify it.

To improve performance, [Yonn Trimoreau](https://www.linkedin.com/in/yonn-trimoreau-3a9856110/) developed a patch for the [colinmollenhour/php-redis-session-abstract](https://github.com/colinmollenhour/php-redis-session-abstract) library used by Magento 2, which restricts the use of locks to write operations only. This significantly reduces latency in high-concurrency scenarios.

This repository includes a customized version of this patch, compatible with Magento 2.4.8 and version 2.1.2 of the aforementioned library.

## Acknowledgments

- To [Colin Mollenhour](https://www.linkedin.com/in/colinmollenhour/) for creating and maintaining this library for the entire PHP community.
- To [Rostislav Suleimanov](https://www.linkedin.com/in/rostilos/) Who has masterfully explained the problem and the different options to mitigate it.
- And finally to [Yonn Trimoreau](https://www.linkedin.com/in/yonn-trimoreau-3a9856110/) who has dedicated his time to solve the problem and has created the patch that I have adapted.

## Recommended reading

- [Magento 2 Redis Session Storage: Performance Issues and Solutions](https://www.linkedin.com/pulse/magento-2-redis-session-storage-performance-issues-suleimanov-qfdae/)
- [Implement write lock instead of read lock](https://github.com/colinmollenhour/php-redis-session-abstract/issues/50)
- [Unnecessary Redis Session Locking On All HTTP GET Requests - Affecting PWA Studio Concurrent GraphQL Requests](https://github.com/magento/magento2/issues/34758#issuecomment-1312524791)
- [Use Redis for session storage](https://experienceleague.adobe.com/en/docs/commerce-operations/configuration-guide/cache/redis/redis-session)
