# Project-_zidio
controllers/orderControllers.js
+21
-4
Lines changed: 21 additions & 4 deletions
Original file line number	Diff line number	Diff line change
@@ -1,15 +1,32 @@
const Order = require('../models/Order');
const Cart = require('../models/Cart');
const User = require('../models/User');
const { createOrder } = require('../helpers/paypal-api');

module.exports.getOrders = async (req, res) => {
module.exports.getOrder = async (req, res) => {
  const userId = req.params.id;
  Order.find({ userId })
    .sort({ date: -1 })
    .then(orders => res.json(orders));
};

module.exports.checkout = async (req, res) => {
  const userId = req.params.id;
  console.log(userId);
module.exports.createOrders = async (req, res) => {
  const data = req.body;
  const { userId, cartId } = req.body;
  const userCart = await Cart.findById({ _id: cartId });
  const cartUserId = userCart.userId.slice(1);
  //verify if cart is associated to the specific user for security purposes
  if (cartUserId === userId) {
    const amountValue = await userCart.bill;
    try {
      const order = await createOrder(amountValue);
      res.json(order);
    } catch (err) {
      res.status(500).send(err.message);
    }
  } else {
    res.status(500);
  }
};
