# python-billing-system
# ===== BILLING SYSTEM =====

import datetime
import random

# ─── Helper Functions ─────────────────────────────────────────

def get_float(prompt):
    while True:
        try:
            value = float(input(prompt))
            if value < 0:
                print("  ⚠ Value cannot be negative. Try again.")
                continue
            return value
        except ValueError:
            print("  ⚠ Invalid input. Enter a number.")

def get_int(prompt):
    while True:
        try:
            value = int(input(prompt))
            if value <= 0:
                print("  ⚠ Quantity must be at least 1.")
                continue
            return value
        except ValueError:
            print("  ⚠ Invalid input. Enter a whole number.")

# ─── Bill Info ────────────────────────────────────────────────

print("=" * 45)
print(f"{'BILLING SYSTEM':^45}")
print("=" * 45)

bill_number  = random.randint(1000, 9999)
now          = datetime.datetime.now()
date_str     = now.strftime("%d %b %Y")
time_str     = now.strftime("%I:%M %p")

customer_name  = input("\nCustomer Name  : ").strip() or "Walk-in Customer"
customer_phone = input("Phone (optional): ").strip()

# ─── Collect Items ────────────────────────────────────────────

items       = []
grand_total = 0.0

print("\nEnter items one by one. Type 'done' to finish.\n")

while True:
    name = input("Item Name (or 'done'): ").strip()
    if name.lower() == "done":
        if not items:
            print("  ⚠ No items added yet. Please add at least one.")
            continue
        break
    if not name:
        print("  ⚠ Item name cannot be empty.")
        continue

    price = get_float("  Price (₹): ")
    qty   = get_int("  Quantity : ")
    total = price * qty

    items.append({"name": name, "price": price, "qty": qty, "total": total})
    grand_total += total
    print(f"  ✔ Added: {name} × {qty} = ₹{total:.2f}\n")

# ─── Discount ─────────────────────────────────────────────────

discount_pct    = get_float("\nEnter discount (% — enter 0 for none): ")
discount_amount = round(grand_total * discount_pct / 100, 2)
after_discount  = round(grand_total - discount_amount, 2)

# ─── Calculate Tax ────────────────────────────────────────────

GST_RATE     = 0.18
gst          = round(after_discount * GST_RATE, 2)
final_amount = round(after_discount + gst, 2)

# ─── Build Receipt Lines ──────────────────────────────────────

DIVIDER = "─" * 45

lines = []
lines.append(f"{'BILL RECEIPT':^45}")
lines.append(DIVIDER)
lines.append(f"  Bill No   : #{bill_number}")
lines.append(f"  Date      : {date_str}")
lines.append(f"  Time      : {time_str}")
lines.append(f"  Customer  : {customer_name}")
if customer_phone:
    lines.append(f"  Phone     : {customer_phone}")
lines.append(DIVIDER)
lines.append(f"{'Item':<18} {'Price':>7} {'Qty':>4} {'Total':>10}")
lines.append(DIVIDER)

for item in items:
    lines.append(
        f"{item['name']:<18} ₹{item['price']:>6.2f} {item['qty']:>4}  ₹{item['total']:>8.2f}"
    )

lines.append(DIVIDER)
lines.append(f"{'Subtotal':<34} ₹{grand_total:>8.2f}")

if discount_pct > 0:
    lines.append(f"{'Discount (' + str(discount_pct) + '%)':<34} -₹{discount_amount:>7.2f}")

lines.append(f"{'GST (18%)':<34} ₹{gst:>8.2f}")
lines.append(DIVIDER)
lines.append(f"{'FINAL AMOUNT':<34} ₹{final_amount:>8.2f}")
lines.append(DIVIDER)
lines.append(f"{'Thank you for your purchase!':^45}")

# ─── Print Receipt ────────────────────────────────────────────

print()
for line in lines:
    print(line)

# ─── Save to File ─────────────────────────────────────────────

filename = f"bill_{bill_number}.txt"

with open(filename, "w", encoding="utf-8") as f:
    for line in lines:
        f.write(line + "\n")

print(f"\n✔ Bill saved to {filename}")
