Memcached
=========

**Step 1:** Set up ``your_ethna_project/etc/php.ini``

.. code-block:: php

   <?php
   'memcache_host' => 'localhost',
   'memcache_port' => 11211,
   'memcache_use_pconnect' => false,
   'memcache_retry' => 3,
   'memcache_timeout' => 3,

**Step 2:** Creating Memcache object in Class

.. code-block:: php

   <?php
   class Php_Action_SomeAction extends Php_ActionClass
   		public function __construct($backend)
    	{
        	$this->cache =& $backend->plugin->getPlugin('Cachemanager', 'Memcache');
        	$this->cache->setNamespace("some_action");

        	parent::__construct($backend);
    	}

In the above code, you can set ``setNamespace`` for your memcache. In the sense
it adds a prefix to all the cache keys that will be set by ``$this->cache`` in this class. Its better that ways if you are going to be dealing thousands of keys on a large system.


**Step 3:** After Memcache Object has been set, then you are able to make your own methods within the class to store data.

.. code-block:: php

   <?php
   private function setCache($urls, $thumbs)
   {
        $data = array('urls' => $urls, 'thumbs' => $thumbs);
        $this->cache->set($this->member, $data, 3600); // 24 hours expiration
   }

   /**
    * Cache key from Member Name e.g. Johny
    * @return array cache value from key
    */
   private function getCache()
   {
   	    $cache = $this->cache->get($this->member); //e.g Johny
        if (PEAR::isError($cache)) {
            return null;
        } else {
            return $cache;
        }
    }

**Step 4:** Verifying ?

.. code-block:: bash

   [bash] memcached-tool localhost:11211 dump|grep Johny
    	  add some_action::Johny 3

.. Tip::

 	In the above example of getCache, I have used ``PEAR::isError``. However, if you look closely in your ``usr/share/pear/Ethna/class/Plugin/Cachemanager/Ethna_Plugin_Cachemanager_Memcache.php``, you may find that it is throwing Ethna:: Error instead of PEAR. Its better to know anyways that if you wanna change that just edit every where it appears. Example:


.. code-block:: php

   146     function get($key, $lifetime = null, $namespace = null)
   147     {
   148         $this->_getMemcache($key, $namespace);
   149         if ($this->memcache == null) {
   150             return PEAR::raiseError('memcache server not available', E_CACHE_NO_VALUE);


