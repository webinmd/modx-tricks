<?php

$status = 1;
$dateLimit = strtotime('-24 hours'); // старше 24 часов
// $dateLimit = strtotime('2025-10-28'); // с начала до какой-то даты


$criteria = $modx->newQuery('msOrder');
$criteria->where([
    'status' => $status,
    'createdon:<=' => date('Y-m-d H:i:s', $dateLimit)
]);

$orders = $modx->getIterator('msOrder', $criteria);

foreach ($orders as $order) {
    $orderId = $order->get('id');
    if (!$order->remove()) {
        $modx->log(modX::LOG_LEVEL_ERROR, "Failed to remove order with ID: $orderId");
        continue;
    }
    $modx->log(modX::LOG_LEVEL_INFO, "Successfully removed order with ID: $orderId");
}
