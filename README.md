# Webservice-1.5-ruby

Integração Ruby com o Webservice 1.5 da Cielo

## Instalação

```
gem install cielo-ws15
```

## Criação de uma transação

```ruby
require "cielo/ws15"

merchant_id = "1006993069"
merchant_key = "25fbb99741c739dd84d7b06ec78c9bac718838630f30b112d033ce2e621b34f3"
environment = Cielo::WS15::TEST

cielo = Cielo::WS15.new(merchant_id, merchant_key, environment)

holder = cielo.holder("4012001037141112", 2018, 5, Cielo::Holder::CVV_INFORMED, 123);
holder.name = "Fulano Portador da Silva"

order = cielo.order("213434", 10000);
payment_method = cielo.payment_method(Cielo::PaymentMethod::VISA,
                                      Cielo::PaymentMethod::CREDITO_A_VISTA)

transaction = cielo.transaction_request(cielo.transaction(
  holder,
  order,
  payment_method,
  "http://localhost/cielo.php",
  Cielo::Transaction::AUTHORIZE_WITHOUT_AUTHENTICATION,
  capture: false
))

puts "TID: " + transaction.tid
puts "PAN: " + transaction.pan

puts "\n\tDados pedido"
puts "\t\tnumero:        " + transaction.order.number
puts "\t\tvalor:         " + transaction.order.total.to_s
puts "\t\tmoeda:         " + transaction.order.currency.to_s
puts "\t\tdata-hora:     " + transaction.order.date_time
puts "\t\tdescrição:     " + transaction.order.description
puts "\t\tidioma:        " + transaction.order.language
puts "\t\ttaxa-embarque: " + transaction.order.shipping.to_s

puts "\n\tForma pagamento"

puts "\t\tbandeira:      " + transaction.payment_method.issuer
puts "\t\tproduto:       " + transaction.payment_method.product
puts "\t\tparcelas:      " + transaction.payment_method.installments.to_s

puts "\n\tAutorização"

puts "\t\tcódigo:        " + transaction.authorization.code
puts "\t\tmensagem:      " + transaction.authorization.message
puts "\t\tdata-hora:     " + transaction.authorization.date_time
puts "\t\tvalor:         " + transaction.authorization.total.to_s
puts "\t\tlr:            " + transaction.authorization.lr
puts "\t\tarp:           " + transaction.authorization.arp
puts "\t\tnsu:           " + transaction.authorization.nsu
```

## Capturando uma transação - total

```ruby
require "cielo/ws15"

merchant_id = "1006993069"
merchant_key = "25fbb99741c739dd84d7b06ec78c9bac718838630f30b112d033ce2e621b34f3"
environment = Cielo::WS15::TEST

cielo = Cielo::WS15.new(merchant_id, merchant_key, environment)

holder = cielo.holder("4012001037141112", 2018, 5, Cielo::Holder::CVV_INFORMED, 123);
holder.name = "Fulano Portador da Silva"

order = cielo.order("213434", 10000);
payment_method = cielo.payment_method(Cielo::PaymentMethod::VISA,
                                      Cielo::PaymentMethod::CREDITO_A_VISTA)

transaction = cielo.transaction(
  holder,
  order,
  payment_method,
  "http://localhost/cielo.php",
  Cielo::Transaction::AUTHORIZE_WITHOUT_AUTHENTICATION
)

transaction.tid = "100699306908642E1001"

transaction = cielo.capture_request(transaction)

puts "\n\tCaptura"

puts "\t\tcódigo:        " + transaction.capture.code
puts "\t\tmensagem:      " + transaction.capture.message
puts "\t\tdata-hora:     " + transaction.capture.date_time
puts "\t\tvalor:         " + transaction.capture.total.to_s
```

## Capturando uma transação - parcial

```ruby
require "cielo/ws15"

merchant_id = "1006993069"
merchant_key = "25fbb99741c739dd84d7b06ec78c9bac718838630f30b112d033ce2e621b34f3"
environment = Cielo::WS15::TEST

cielo = Cielo::WS15.new(merchant_id, merchant_key, environment)

holder = cielo.holder("4012001037141112", 2018, 5, Cielo::Holder::CVV_INFORMED, 123);
holder.name = "Fulano Portador da Silva"

order = cielo.order("213434", 10000);
payment_method = cielo.payment_method(Cielo::PaymentMethod::VISA,
                                      Cielo::PaymentMethod::CREDITO_A_VISTA)

transaction = cielo.transaction(
  holder,
  order,
  payment_method,
  "http://localhost/cielo.php",
  Cielo::Transaction::AUTHORIZE_WITHOUT_AUTHENTICATION
)

transaction.tid = "100699306908642E1001"

transaction = cielo.capture_request(transaction, 5000)

puts "\n\tCaptura"

puts "\t\tcódigo:        " + transaction.capture.code
puts "\t\tmensagem:      " + transaction.capture.message
puts "\t\tdata-hora:     " + transaction.capture.date_time
puts "\t\tvalor:         " + transaction.capture.total.to_s
```

## Criação de uma transação com captura automática

```ruby
require "cielo/ws15"

merchant_id = "1006993069"
merchant_key = "25fbb99741c739dd84d7b06ec78c9bac718838630f30b112d033ce2e621b34f3"
environment = Cielo::WS15::TEST

cielo = Cielo::WS15.new(merchant_id, merchant_key, environment)

holder = cielo.holder("4012001037141112", 2018, 5, Cielo::Holder::CVV_INFORMED, 123);
holder.name = "Fulano Portador da Silva"

order = cielo.order("213434", 10000);
payment_method = cielo.payment_method(Cielo::PaymentMethod::VISA,
                                      Cielo::PaymentMethod::CREDITO_A_VISTA)

transaction = cielo.transaction_request(cielo.transaction(
  holder,
  order,
  payment_method,
  "http://localhost/cielo.php",
  Cielo::Transaction::AUTHORIZE_WITHOUT_AUTHENTICATION,
  capture: true
))
```

## Criação de um Token

```ruby
require "cielo/ws15"

merchant_id = "1006993069"
merchant_key = "25fbb99741c739dd84d7b06ec78c9bac718838630f30b112d033ce2e621b34f3"
environment = Cielo::WS15::TEST

cielo = Cielo::WS15.new(merchant_id, merchant_key, environment)

holder = cielo.holder("4012001037141112", 2018, 5, Cielo::Holder::CVV_INFORMED, 123);
holder.name = "Fulano Portador da Silva"

token = cielo.token_request(holder)

puts "\n\tToken"
puts "\t\tcódigo: " + token.code
puts "\t\tstatus: " + token.status
puts "\t\tnúmero: " + token.number
```

## Transação com Token

```ruby
require "cielo/ws15"

merchant_id = "1006993069"
merchant_key = "25fbb99741c739dd84d7b06ec78c9bac718838630f30b112d033ce2e621b34f3"
environment = Cielo::WS15::TEST

cielo = Cielo::WS15.new(merchant_id, merchant_key, environment)

holder = cielo.holder("Q6zDYxwrvJuqpeJMdpEfdTb8b++F++h3N1VGfZU3nVw=");

transaction = cielo.transaction_request(cielo.transaction(
  holder,
  order,
  payment_method,
  "http://localhost/cielo.php",
  Cielo::Transaction::AUTHORIZE_WITHOUT_AUTHENTICATION
))
```

## Cancelando uma transação - total

```ruby
require "cielo/ws15"

merchant_id = "1006993069"
merchant_key = "25fbb99741c739dd84d7b06ec78c9bac718838630f30b112d033ce2e621b34f3"
environment = Cielo::WS15::TEST

cielo = Cielo::WS15.new(merchant_id, merchant_key, environment)

holder = cielo.holder("4012001037141112", 2018, 5, Cielo::Holder::CVV_INFORMED, 123);
holder.name = "Fulano Portador da Silva"

order = cielo.order("213434", 10000);
payment_method = cielo.payment_method(Cielo::PaymentMethod::VISA,
                                      Cielo::PaymentMethod::CREDITO_A_VISTA)

transaction = cielo.transaction(
  holder,
  order,
  payment_method,
  "http://localhost/cielo.php",
  Cielo::Transaction::AUTHORIZE_WITHOUT_AUTHENTICATION
)

transaction.tid = "100699306908642E1001"

transaction = cielo.cancellation_request(transaction)

transaction.cancellation.each do |cancellation|
  puts "\t\t\tcódigo:        " + cancellation.code
  puts "\t\t\tmensagem:      " + cancellation.message
  puts "\t\t\tdata-hora:     " + cancellation.date_time
  puts "\t\t\tvalor:         " + cancellation.total.to_s
end
```

## Cancelando uma transação - parcial

```ruby
require "cielo/ws15"

merchant_id = "1006993069"
merchant_key = "25fbb99741c739dd84d7b06ec78c9bac718838630f30b112d033ce2e621b34f3"
environment = Cielo::WS15::TEST

cielo = Cielo::WS15.new(merchant_id, merchant_key, environment)

holder = cielo.holder("4012001037141112", 2018, 5, Cielo::Holder::CVV_INFORMED, 123);
holder.name = "Fulano Portador da Silva"

order = cielo.order("213434", 10000);
payment_method = cielo.payment_method(Cielo::PaymentMethod::VISA,
                                      Cielo::PaymentMethod::CREDITO_A_VISTA)

transaction = cielo.transaction(
  holder,
  order,
  payment_method,
  "http://localhost/cielo.php",
  Cielo::Transaction::AUTHORIZE_WITHOUT_AUTHENTICATION
)

transaction.tid = "100699306908642E1001"

transaction = cielo.cancellation_request(transaction, 5000)

transaction.cancellation.each do |cancellation|
  puts "\t\t\tcódigo:        " + cancellation.code
  puts "\t\t\tmensagem:      " + cancellation.message
  puts "\t\t\tdata-hora:     " + cancellation.date_time
  puts "\t\t\tvalor:         " + cancellation.total.to_s
end
```
