//OPTIMISED CODE FOR CHECKOUT..
import cart
import products
from cart import get_cart
import os

def checkout(username):
    cart = get_cart(username)
    total = 0
    # for item in cart:
    #     while(item.cost > 0):
    #         total += 1
    #         item.cost -= 1
    for item in cart:
        total += item.cost

    #Here the exit can happen when a illegal memory is accessed 
    # or when a error is not handled properly
   # os._exit(1)
    return total


def complete_checkout(username):
    cartv = cart.get_cart(username)
    items = cartv
    for item in items:
        assert item.qty >= 1
    for item in items:
        cart.delete_cart(username)
        products.update_qty(item.id, item.qty-1)




//OPTIMISED CODE FOR CART..

import json
from typing import List, Optional

import products
from cart import dao
from products import Product


class Cart:
    def __init__(self, id: int, username: str, contents: List[Product], cost: float):
        self.id = id
        self.username = username
        self.contents = contents
        self.cost = cost

    @staticmethod
    def load(data: dict) -> "Cart":
        return Cart(
            id=data["id"],
            username=data["username"],
            contents=[Product(**item) for item in json.loads(data["contents"])],
            cost=data["cost"],
        )


def get_cart(username: str) -> List[Product]:
    cart_details = dao.get_cart(username)
    if not cart_details:
        return []

    all_products = []
    for cart_detail in cart_details:
        contents = json.loads(cart_detail.get("contents", "[]"))
        all_products.extend(contents)

    # Fetch product details for all items in a single operation if possible
    return [products.get_product(item_id) for item_id in all_products]


def add_to_cart(username: str, product_id: int) -> None:
    dao.add_to_cart(username, product_id)


def remove_from_cart(username: str, product_id: int) -> None:
    dao.remove_from_cart(username, product_id)


def delete_cart(username: str) -> None:
    dao.delete_cart(username)
