<?php
class Singleton {
    private static $instance = null;

    private function __construct() {
        echo "Init Singleton\n";
    }

    // 외부에서 new 금지
    private function __clone() {}
    private function __wakeup() {}

    public static function getInstance() {
        if (self::$instance === null) {
            self::$instance = new Singleton();
        }
        return self::$instance;
    }
}

$obj1 = Singleton::getInstance();
$obj2 = Singleton::getInstance();

var_dump($obj1 === $obj2); // true
