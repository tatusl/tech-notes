# Shovel plugin

Shovel plugin allows to move messages from one broker to another. Source and destination can be queue or exchange.

* Shoveling all messages from one exchange to another
  * Source routing key should be `#` (wildcard)
  * Destination routing key is left empty