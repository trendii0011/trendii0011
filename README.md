import os
import json
import logging
from datetime import datetime
from decimal import Decimal
from typing import Dict, List, Optional
import asyncio

from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import (
Application,
CommandHandler,
CallbackQueryHandler,
MessageHandler,
ContextTypes,
filters,
)

# Configure logging

logging.basicConfig(
format=’%(asctime)s - %(name)s - %(levelname)s - %(message)s’,
level=logging.INFO
)
logger = logging.getLogger(**name**)

# Constants

ADMIN_IDS = []  # Add admin user IDs here
DATA_FILE = ‘user_data.json’

class TrojanBot:
“”“Main Telegram Trading Bot for Solana”””

```
def __init__(self):
    self.user_wallets = {}
    self.user_settings = {}
    self.user_positions = {}
    self.load_data()

def load_data(self):
    """Load user data from file"""
    try:
        if os.path.exists(DATA_FILE):
            with open(DATA_FILE, 'r') as f:
                data = json.load(f)
                self.user_wallets = data.get('wallets', {})
                self.user_settings = data.get('settings', {})
                self.user_positions = data.get('positions', {})
    except Exception as e:
        logger.error(f"Error loading data: {e}")

def save_data(self):
    """Save user data to file"""
    try:
        data = {
            'wallets': self.user_wallets,
            'settings': self.user_settings,
            'positions': self.user_positions
        }
        with open(DATA_FILE, 'w') as f:
            json.dump(data, f, indent=2)
    except Exception as e:
        logger.error(f"Error saving data: {e}")

def get_user_settings(self, user_id: str) -> Dict:
    """Get user settings with defaults"""
    if user_id not in self.user_settings:
        self.user_settings[user_id] = {
            'slippage': 1.0,
            'gas_priority': 'medium',
            'mev_protection': True,
            'auto_approve': False,
            'notifications': True,
            'auto_buy_amount': 0.1,
            'take_profit': 50.0,
            'stop_loss': 25.0,
            'trailing_stop': False,
            'trailing_percent': 10.0
        }
        self.save_data()
    return self.user_settings[user_id]
```

bot_instance = TrojanBot()

# Keyboard Layouts

def get_main_menu_keyboard():
“”“Main menu keyboard”””
keyboard = [
[
InlineKeyboardButton(“💼 Wallet”, callback_data=‘wallet’),
InlineKeyboardButton(“📊 Positions”, callback_data=‘positions’)
],
[
InlineKeyboardButton(“🔍 Find Token”, callback_data=‘find_token’),
InlineKeyboardButton(“⚡ Quick Buy”, callback_data=‘quick_buy’)
],
[
InlineKeyboardButton(“🤖 Auto Trade”, callback_data=‘auto_trade’),
InlineKeyboardButton(“📈 Analytics”, callback_data=‘analytics’)
],
[
InlineKeyboardButton(“⚙️ Settings”, callback_data=‘settings’),
InlineKeyboardButton(“❓ Help”, callback_data=‘help’)
],
[
InlineKeyboardButton(“🔄 Refresh”, callback_data=‘refresh’)
]
]
return InlineKeyboardMarkup(keyboard)

def get_wallet_keyboard():
“”“Wallet management keyboard”””
keyboard = [
[
InlineKeyboardButton(“💰 View Balance”, callback_data=‘view_balance’),
InlineKeyboardButton(“📥 Deposit”, callback_data=‘deposit’)
],
[
InlineKeyboardButton(“📤 Withdraw”, callback_data=‘withdraw’),
InlineKeyboardButton(“🔑 Export Key”, callback_data=‘export_key’)
],
[
InlineKeyboardButton(“🔄 New Wallet”, callback_data=‘new_wallet’),
InlineKeyboardButton(“📋 Import Wallet”, callback_data=‘import_wallet’)
],
[
InlineKeyboardButton(“◀️ Back”, callback_data=‘main_menu’)
]
]
return InlineKeyboardMarkup(keyboard)

def get_token_action_keyboard(token_address: str):
“”“Token action keyboard”””
keyboard = [
[
InlineKeyboardButton(“💚 Buy”, callback_data=f’buy_{token_address}’),
InlineKeyboardButton(“💔 Sell”, callback_data=f’sell_{token_address}’)
],
[
InlineKeyboardButton(“📊 Chart”, callback_data=f’chart_{token_address}’),
InlineKeyboardButton(“ℹ️ Info”, callback_data=f’info_{token_address}’)
],
[
InlineKeyboardButton(“🔔 Set Alerts”, callback_data=f’alert_{token_address}’),
InlineKeyboardButton(“⭐ Add to Favorites”, callback_data=f’fav_{token_address}’)
],
[
InlineKeyboardButton(“◀️ Back”, callback_data=‘main_menu’)
]
]
return InlineKeyboardMarkup(keyboard)

def get_buy_amount_keyboard():
“”“Quick buy amount keyboard”””
keyboard = [
[
InlineKeyboardButton(“0.1 SOL”, callback_data=‘buyamt_0.1’),
InlineKeyboardButton(“0.5 SOL”, callback_data=‘buyamt_0.5’)
],
[
InlineKeyboardButton(“1 SOL”, callback_data=‘buyamt_1’),
InlineKeyboardButton(“2 SOL”, callback_data=‘buyamt_2’)
],
[
InlineKeyboardButton(“5 SOL”, callback_data=‘buyamt_5’),
InlineKeyboardButton(“✏️ Custom”, callback_data=‘buyamt_custom’)
],
[
InlineKeyboardButton(“◀️ Back”, callback_data=‘main_menu’)
]
]
return InlineKeyboardMarkup(keyboard)

def get_sell_percentage_keyboard():
“”“Sell percentage keyboard”””
keyboard = [
[
InlineKeyboardButton(“25%”, callback_data=‘sell_25’),
InlineKeyboardButton(“50%”, callback_data=‘sell_50’)
],
[
InlineKeyboardButton(“75%”, callback_data=‘sell_75’),
InlineKeyboardButton(“100%”, callback_data=‘sell_100’)
],
[
InlineKeyboardButton(“✏️ Custom Amount”, callback_data=‘sell_custom’)
],
[
InlineKeyboardButton(“◀️ Back”, callback_data=‘positions’)
]
]
return InlineKeyboardMarkup(keyboard)

def get_settings_keyboard():
“”“Settings keyboard”””
keyboard = [
[
InlineKeyboardButton(“💹 Slippage”, callback_data=‘set_slippage’),
InlineKeyboardButton(“⚡ Gas Priority”, callback_data=‘set_gas’)
],
[
InlineKeyboardButton(“🛡️ MEV Protection”, callback_data=‘toggle_mev’),
InlineKeyboardButton(“✅ Auto Approve”, callback_data=‘toggle_approve’)
],
[
InlineKeyboardButton(“🔔 Notifications”, callback_data=‘toggle_notifications’),
InlineKeyboardButton(“🎯 TP/SL Settings”, callback_data=‘set_tpsl’)
],
[
InlineKeyboardButton(“📊 View Settings”, callback_data=‘view_settings’)
],
[
InlineKeyboardButton(“◀️ Back”, callback_data=‘main_menu’)
]
]
return InlineKeyboardMarkup(keyboard)

def get_auto_trade_keyboard():
“”“Auto trade settings keyboard”””
keyboard = [
[
InlineKeyboardButton(“🚀 Auto Buy New Tokens”, callback_data=‘toggle_auto_buy’),
InlineKeyboardButton(“🎯 Take Profit”, callback_data=‘set_take_profit’)
],
[
InlineKeyboardButton(“🛑 Stop Loss”, callback_data=‘set_stop_loss’),
InlineKeyboardButton(“📉 Trailing Stop”, callback_data=‘toggle_trailing’)
],
[
InlineKeyboardButton(“📋 View Auto Rules”, callback_data=‘view_auto_rules’),
InlineKeyboardButton(“🔄 Toggle All”, callback_data=‘toggle_all_auto’)
],
[
InlineKeyboardButton(“◀️ Back”, callback_data=‘main_menu’)
]
]
return InlineKeyboardMarkup(keyboard)

# Command Handlers

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
“”“Start command handler”””
user = update.effective_user
user_id = str(user.id)

```
# Initialize user wallet if not exists
if user_id not in bot_instance.user_wallets:
    # In production, generate real Solana wallet
    bot_instance.user_wallets[user_id] = {
        'address': f'DEMO{user_id}',
        'balance_sol': 0.0,
        'created_at': datetime.now().isoformat()
    }
    bot_instance.save_data()

welcome_text = f"""
```

🏛️ **Welcome to Trojan on Solana** 🏛️

Hello {user.mention_html()}!

⚡ **Solana’s Fastest Trading Bot**

Trade any SPL token with lightning speed:
• 💰 Buy & Sell tokens instantly
• 🤖 Automated trading strategies
• 📊 Real-time portfolio tracking
• 🛡️ MEV protection enabled
• ⚡ Priority gas optimization

**Your Wallet:**
`{bot_instance.user_wallets[user_id]['address']}`

Use the menu below to get started! 👇
“””

```
await update.message.reply_html(
    welcome_text,
    reply_markup=get_main_menu_keyboard()
)
```

async def help_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
“”“Help command handler”””
help_text = “””
📚 **Trojan Bot Commands & Features**

**Main Commands:**
/start - Start the bot
/wallet - Manage your wallet
/buy <address> - Buy a token
/sell <address> - Sell a token
/positions - View open positions
/settings - Configure settings
/help - Show this help

**Quick Actions:**
• Use /buy to instantly purchase tokens
• Set up auto-trading with /auto
• Monitor positions with /positions
• Adjust slippage in /settings

**Features:**
🔹 Lightning-fast execution
🔹 Auto buy/sell strategies
🔹 Take profit & stop loss
🔹 MEV protection
🔹 Real-time notifications
🔹 Portfolio analytics

**Support:**
Join our community: @trojan_on_solana
Report issues: Contact admins

Happy Trading! 🚀
“””

```
keyboard = [[InlineKeyboardButton("◀️ Main Menu", callback_data='main_menu')]]

await update.message.reply_html(
    help_text,
    reply_markup=InlineKeyboardMarkup(keyboard)
)
```

async def wallet_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
“”“Wallet command handler”””
user_id = str(update.effective_user.id)
wallet = bot_instance.user_wallets.get(user_id)

```
if not wallet:
    await update.message.reply_text("❌ No wallet found. Use /start to create one.")
    return

wallet_text = f"""
```

💼 **Your Wallet**

**Address:**
`{wallet['address']}`

**Balance:**
💰 {wallet.get(‘balance_sol’, 0):.4f} SOL
💵 ${wallet.get(‘balance_sol’, 0) * 150:.2f} USD

**Holdings:**
{_get_holdings_text(user_id)}

Tap an option below to manage your wallet:
“””

```
await update.message.reply_html(
    wallet_text,
    reply_markup=get_wallet_keyboard()
)
```

def _get_holdings_text(user_id: str) -> str:
“”“Get formatted holdings text”””
positions = bot_instance.user_positions.get(user_id, {})

```
if not positions:
    return "No token holdings"

holdings = []
for token, data in positions.items():
    amount = data.get('amount', 0)
    value = data.get('value_usd', 0)
    holdings.append(f"• {token}: {amount:.2f} (${value:.2f})")

return "\n".join(holdings)
```

async def positions_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
“”“Positions command handler”””
user_id = str(update.effective_user.id)
positions = bot_instance.user_positions.get(user_id, {})

```
if not positions:
    positions_text = """
```

📊 **Your Positions**

You don’t have any open positions yet.

Start trading to see your positions here!
“””
else:
positions_text = “📊 **Your Open Positions**\n\n”
total_value = 0

```
    for token, data in positions.items():
        amount = data.get('amount', 0)
        entry_price = data.get('entry_price', 0)
        current_price = data.get('current_price', entry_price)
        value = amount * current_price
        pnl = ((current_price - entry_price) / entry_price) * 100 if entry_price > 0 else 0
        pnl_emoji = "🟢" if pnl >= 0 else "🔴"
        
        positions_text += f"""
```

**{token}**
• Amount: {amount:.4f}
• Entry: ${entry_price:.6f}
• Current: ${current_price:.6f}
• Value: ${value:.2f}
• P&L: {pnl_emoji} {pnl:+.2f}%

“””
total_value += value

```
    positions_text += f"\n💰 **Total Portfolio Value:** ${total_value:.2f}"

keyboard = [
    [InlineKeyboardButton("🔄 Refresh", callback_data='positions')],
    [InlineKeyboardButton("◀️ Main Menu", callback_data='main_menu')]
]

await update.message.reply_html(
    positions_text,
    reply_markup=InlineKeyboardMarkup(keyboard)
)
```

async def buy_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
“”“Buy command handler”””
if not context.args:
await update.message.reply_text(
“❌ Please provide a token address.\n\n”
“Usage: /buy <token_address>”
)
return

```
token_address = context.args[0]

# Validate token address (basic check)
if len(token_address) < 32:
    await update.message.reply_text("❌ Invalid token address")
    return

buy_text = f"""
```

💚 **Buy Token**

**Token Address:**
`{token_address}`

🔍 Fetching token information…

Select amount to buy:
“””

```
await update.message.reply_html(
    buy_text,
    reply_markup=get_buy_amount_keyboard()
)
```

async def sell_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
“”“Sell command handler”””
if not context.args:
await update.message.reply_text(
“❌ Please provide a token address.\n\n”
“Usage: /sell <token_address>”
)
return

```
token_address = context.args[0]
user_id = str(update.effective_user.id)

# Check if user has this token
positions = bot_instance.user_positions.get(user_id, {})

sell_text = f"""
```

💔 **Sell Token**

**Token Address:**
`{token_address}`

Select percentage to sell:
“””

```
await update.message.reply_html(
    sell_text,
    reply_markup=get_sell_percentage_keyboard()
)
```

# Callback Query Handlers

async def button_callback(update: Update, context: ContextTypes.DEFAULT_TYPE):
“”“Handle button callbacks”””
query = update.callback_query
await query.answer()

```
user_id = str(query.from_user.id)
data = query.data

# Main menu
if data == 'main_menu':
    await show_main_menu(query, user_id)

# Wallet actions
elif data == 'wallet':
    await show_wallet(query, user_id)
elif data == 'view_balance':
    await view_balance(query, user_id)
elif data == 'deposit':
    await show_deposit(query, user_id)
elif data == 'withdraw':
    await show_withdraw(query, user_id)
elif data == 'export_key':
    await export_private_key(query, user_id)
elif data == 'new_wallet':
    await create_new_wallet(query, user_id)

# Trading actions
elif data == 'find_token':
    await find_token(query, user_id)
elif data == 'quick_buy':
    await quick_buy_menu(query, user_id)
elif data == 'positions':
    await show_positions(query, user_id)

# Buy amounts
elif data.startswith('buyamt_'):
    amount = data.split('_')[1]
    await process_buy_amount(query, user_id, amount)

# Sell percentages
elif data.startswith('sell_'):
    percentage = data.split('_')[1]
    await process_sell_percentage(query, user_id, percentage)

# Settings
elif data == 'settings':
    await show_settings(query, user_id)
elif data == 'set_slippage':
    await set_slippage(query, user_id)
elif data == 'set_gas':
    await set_gas_priority(query, user_id)
elif data == 'toggle_mev':
    await toggle_mev_protection(query, user_id)
elif data == 'toggle_approve':
    await toggle_auto_approve(query, user_id)
elif data == 'toggle_notifications':
    await toggle_notifications(query, user_id)
elif data == 'view_settings':
    await view_settings(query, user_id)

# Auto trade
elif data == 'auto_trade':
    await show_auto_trade(query, user_id)
elif data == 'toggle_auto_buy':
    await toggle_auto_buy(query, user_id)
elif data == 'set_take_profit':
    await set_take_profit(query, user_id)
elif data == 'set_stop_loss':
    await set_stop_loss(query, user_id)
elif data == 'toggle_trailing':
    await toggle_trailing_stop(query, user_id)
elif data == 'view_auto_rules':
    await view_auto_rules(query, user_id)

# Analytics
elif data == 'analytics':
    await show_analytics(query, user_id)

# Help
elif data == 'help':
    await show_help(query, user_id)

# Refresh
elif data == 'refresh':
    await show_main_menu(query, user_id)
```

# Callback Functions

async def show_main_menu(query, user_id: str):
“”“Show main menu”””
wallet = bot_instance.user_wallets.get(user_id, {})
balance = wallet.get(‘balance_sol’, 0)

```
menu_text = f"""
```

🏛️ **Trojan on Solana**

💼 **Balance:** {balance:.4f} SOL

⚡ Ready to trade!
“””

```
await query.edit_message_text(
    menu_text,
    reply_markup=get_main_menu_keyboard(),
    parse_mode='Markdown'
)
```

async def show_wallet(query, user_id: str):
“”“Show wallet details”””
wallet = bot_instance.user_wallets.get(user_id)

```
if not wallet:
    await query.edit_message_text("❌ No wallet found")
    return

wallet_text = f"""
```

💼 **Your Wallet**

**Address:**
`{wallet['address']}`

**Balance:**
💰 {wallet.get(‘balance_sol’, 0):.4f} SOL
💵 ${wallet.get(‘balance_sol’, 0) * 150:.2f} USD

**Holdings:**
{_get_holdings_text(user_id)}
“””

```
await query.edit_message_text(
    wallet_text,
    reply_markup=get_wallet_keyboard(),
    parse_mode='Markdown'
)
```

async def view_balance(query, user_id: str):
“”“View detailed balance”””
wallet = bot_instance.user_wallets.get(user_id, {})
positions = bot_instance.user_positions.get(user_id, {})

```
sol_balance = wallet.get('balance_sol', 0)
total_token_value = sum(
    p.get('amount', 0) * p.get('current_price', 0) 
    for p in positions.values()
)

balance_text = f"""
```

💰 **Detailed Balance**

**Wallet:**
• SOL: {sol_balance:.4f} (${sol_balance * 150:.2f})

**Tokens:**
• Value: ${total_token_value:.2f}

**Total Portfolio:**
💎 ${(sol_balance * 150 + total_token_value):.2f}

Last updated: {datetime.now().strftime(’%H:%M:%S’)}
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back to Wallet", callback_data='wallet')]]

await query.edit_message_text(
    balance_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def show_deposit(query, user_id: str):
“”“Show deposit information”””
wallet = bot_instance.user_wallets.get(user_id, {})
address = wallet.get(‘address’, ‘N/A’)

```
deposit_text = f"""
```

📥 **Deposit SOL**

Send SOL to your wallet address:

`{address}`

⚠️ **Important:**
• Only send SOL to this address
• Network: Solana Mainnet
• Minimum: 0.01 SOL
• Funds appear within 1-2 minutes

Your deposit will be credited automatically!
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back", callback_data='wallet')]]

await query.edit_message_text(
    deposit_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def show_withdraw(query, user_id: str):
“”“Show withdrawal interface”””
wallet = bot_instance.user_wallets.get(user_id, {})
balance = wallet.get(‘balance_sol’, 0)

```
withdraw_text = f"""
```

📤 **Withdraw SOL**

**Available Balance:** {balance:.4f} SOL

To withdraw, send:
`/withdraw <amount> <address>`

Example:
`/withdraw 1.5 YourSolanaAddress123...`

⚠️ **Fees:**
• Network fee: ~0.00001 SOL
• Min withdrawal: 0.01 SOL
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back", callback_data='wallet')]]

await query.edit_message_text(
    withdraw_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def export_private_key(query, user_id: str):
“”“Export private key (WARNING in production)”””
warning_text = “””
🔐 **Export Private Key**

⚠️ **SECURITY WARNING**

Exporting your private key is dangerous!

• Never share your private key
• Store it securely offline
• Anyone with your key controls your funds

Are you sure you want to continue?
“””

```
keyboard = [
    [
        InlineKeyboardButton("✅ Yes, Export", callback_data='confirm_export'),
        InlineKeyboardButton("❌ Cancel", callback_data='wallet')
    ]
]

await query.edit_message_text(
    warning_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def create_new_wallet(query, user_id: str):
“”“Create a new wallet”””
# In production, generate real Solana keypair
new_address = f”NEW{user_id}{datetime.now().timestamp()}”

```
bot_instance.user_wallets[user_id] = {
    'address': new_address,
    'balance_sol': 0.0,
    'created_at': datetime.now().isoformat()
}
bot_instance.save_data()

new_wallet_text = f"""
```

✅ **New Wallet Created**

**Address:**
`{new_address}`

**Balance:** 0 SOL

⚠️ Make sure to backup your wallet!

Your previous wallet (if any) has been replaced.
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back to Wallet", callback_data='wallet')]]

await query.edit_message_text(
    new_wallet_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def find_token(query, user_id: str):
“”“Find token interface”””
find_text = “””
🔍 **Find Token**

Send me a token address or symbol to search:

Examples:
• `DezXAZ8z7PnrnRJjz3wXBoRgixCa6xjnB7YaB1pPB263` (BONK)
• `BONK`
• `WIF`

I’ll show you the token details and trading options!
“””

```
keyboard = [[InlineKeyboardButton("◀️ Main Menu", callback_data='main_menu')]]

await query.edit_message_text(
    find_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def quick_buy_menu(query, user_id: str):
“”“Quick buy menu”””
quick_buy_text = “””
⚡ **Quick Buy**

Select amount to buy:

Your last searched token will be purchased immediately.

Search for a token first with /buy <address>
“””

```
await query.edit_message_text(
    quick_buy_text,
    reply_markup=get_buy_amount_keyboard(),
    parse_mode='Markdown'
)
```

async def show_positions(query, user_id: str):
“”“Show positions”””
positions = bot_instance.user_positions.get(user_id, {})

```
if not positions:
    positions_text = """
```

📊 **Your Positions**

No open positions.

Start trading to see your positions here!
“””
else:
positions_text = “📊 **Your Open Positions**\n\n”
total_value = 0

```
    for token, data in positions.items():
        amount = data.get('amount', 0)
        entry_price = data.get('entry_price', 0)
        current_price = data.get('current_price', entry_price)
        value = amount * current_price
        pnl = ((current_price - entry_price) / entry_price) * 100 if entry_price > 0 else 0
        pnl_emoji = "🟢" if pnl >= 0 else "🔴"
        
        positions_text += f"""
```

**{token}**
• Amount: {amount:.4f}
• Entry: ${entry_price:.6f}
• Current: ${current_price:.6f}
• Value: ${value:.2f}
• P&L: {pnl_emoji} {pnl:+.2f}%

“””
total_value += value

```
    positions_text += f"\n💰 **Total:** ${total_value:.2f}"

keyboard = [
    [InlineKeyboardButton("🔄 Refresh", callback_data='positions')],
    [InlineKeyboardButton("◀️ Main Menu", callback_data='main_menu')]
]

await query.edit_message_text(
    positions_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def process_buy_amount(query, user_id: str, amount: str):
“”“Process buy with specific amount”””
if amount == ‘custom’:
custom_text = “””
✏️ **Custom Buy Amount**

Send the amount of SOL you want to spend:

Example: `1.5`

Min: 0.01 SOL
“””
keyboard = [[InlineKeyboardButton(“◀️ Back”, callback_data=‘quick_buy’)]]

```
    await query.edit_message_text(
        custom_text,
        reply_markup=InlineKeyboardMarkup(keyboard),
        parse_mode='Markdown'
    )
    return

# Simulate buy
buy_amount = float(amount)

confirmation_text = f"""
```

✅ **Buy Order Executed**

**Amount:** {buy_amount} SOL
**Token:** DEMO TOKEN
**Price:** $0.00123

🔄 Processing transaction…

Your tokens will appear in your wallet shortly!
“””

```
# Add to positions (demo)
if user_id not in bot_instance.user_positions:
    bot_instance.user_positions[user_id] = {}

bot_instance.user_positions[user_id]['DEMO'] = {
    'amount': buy_amount / 0.00123,
    'entry_price': 0.00123,
    'current_price': 0.00123,
    'timestamp': datetime.now().isoformat()
}
bot_instance.save_data()

keyboard = [
    [InlineKeyboardButton("📊 View Positions", callback_data='positions')],
    [InlineKeyboardButton("◀️ Main Menu", callback_data='main_menu')]
]

await query.edit_message_text(
    confirmation_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def process_sell_percentage(query, user_id: str, percentage: str):
“”“Process sell with percentage”””
if percentage == ‘custom’:
custom_text = “””
✏️ **Custom Sell Amount**

Send the exact amount of tokens to sell:

Example: `1000`
“””
keyboard = [[InlineKeyboardButton(“◀️ Back”, callback_data=‘positions’)]]

```
    await query.edit_message_text(
        custom_text,
        reply_markup=InlineKeyboardMarkup(keyboard),
        parse_mode='Markdown'
    )
    return

sell_pct = int(percentage)

confirmation_text = f"""
```

✅ **Sell Order Executed**

**Percentage:** {sell_pct}%
**Token:** DEMO TOKEN
**Received:** ~{sell_pct / 100 * 1.5:.3f} SOL

🔄 Processing transaction…

Funds will be credited to your wallet!
“””

```
keyboard = [
    [InlineKeyboardButton("📊 View Positions", callback_data='positions')],
    [InlineKeyboardButton("◀️ Main Menu", callback_data='main_menu')]
]

await query.edit_message_text(
    confirmation_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def show_settings(query, user_id: str):
“”“Show settings menu”””
settings_text = “””
⚙️ **Settings**

Configure your trading preferences:

• 💹 Slippage tolerance
• ⚡ Gas priority
• 🛡️ MEV protection
• ✅ Auto-approve trades
• 🔔 Notifications
• 🎯 TP/SL defaults
“””

```
await query.edit_message_text(
    settings_text,
    reply_markup=get_settings_keyboard(),
    parse_mode='Markdown'
)
```

async def set_slippage(query, user_id: str):
“”“Set slippage tolerance”””
settings = bot_instance.get_user_settings(user_id)
current = settings[‘slippage’]

```
slippage_text = f"""
```

💹 **Slippage Tolerance**

Current: **{current}%**

Choose a new slippage tolerance:
“””

```
keyboard = [
    [
        InlineKeyboardButton("0.5%", callback_data='slippage_0.5'),
        InlineKeyboardButton("1%", callback_data='slippage_1')
    ],
    [
        InlineKeyboardButton("2%", callback_data='slippage_2'),
        InlineKeyboardButton("5%", callback_data='slippage_5')
    ],
    [
        InlineKeyboardButton("✏️ Custom", callback_data='slippage_custom')
    ],
    [
        InlineKeyboardButton("◀️ Back", callback_data='settings')
    ]
]

await query.edit_message_text(
    slippage_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def set_gas_priority(query, user_id: str):
“”“Set gas priority”””
settings = bot_instance.get_user_settings(user_id)
current = settings[‘gas_priority’]

```
gas_text = f"""
```

⚡ **Gas Priority**

Current: **{current.capitalize()}**

Higher priority = Faster execution + Higher fees
“””

```
keyboard = [
    [
        InlineKeyboardButton("Low", callback_data='gas_low'),
        InlineKeyboardButton("Medium", callback_data='gas_medium')
    ],
    [
        InlineKeyboardButton("High", callback_data='gas_high'),
        InlineKeyboardButton("Turbo", callback_data='gas_turbo')
    ],
    [
        InlineKeyboardButton("◀️ Back", callback_data='settings')
    ]
]

await query.edit_message_text(
    gas_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def toggle_mev_protection(query, user_id: str):
“”“Toggle MEV protection”””
settings = bot_instance.get_user_settings(user_id)
settings[‘mev_protection’] = not settings[‘mev_protection’]
bot_instance.save_data()

```
status = "✅ Enabled" if settings['mev_protection'] else "❌ Disabled"

toggle_text = f"""
```

🛡️ **MEV Protection**

Status: **{status}**

MEV protection helps prevent:
• Frontrunning
• Sandwich attacks
• Price manipulation

Setting updated!
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back to Settings", callback_data='settings')]]

await query.edit_message_text(
    toggle_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def toggle_auto_approve(query, user_id: str):
“”“Toggle auto approve”””
settings = bot_instance.get_user_settings(user_id)
settings[‘auto_approve’] = not settings[‘auto_approve’]
bot_instance.save_data()

```
status = "✅ Enabled" if settings['auto_approve'] else "❌ Disabled"

toggle_text = f"""
```

✅ **Auto Approve Trades**

Status: **{status}**

When enabled, trades execute immediately without confirmation.

⚠️ Use with caution!
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back to Settings", callback_data='settings')]]

await query.edit_message_text(
    toggle_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def toggle_notifications(query, user_id: str):
“”“Toggle notifications”””
settings = bot_instance.get_user_settings(user_id)
settings[‘notifications’] = not settings[‘notifications’]
bot_instance.save_data()

```
status = "✅ Enabled" if settings['notifications'] else "❌ Disabled"

toggle_text = f"""
```

🔔 **Notifications**

Status: **{status}**

Receive alerts for:
• Trade executions
• Price alerts
• Position updates
• Auto-trade actions
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back to Settings", callback_data='settings')]]

await query.edit_message_text(
    toggle_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def view_settings(query, user_id: str):
“”“View all settings”””
settings = bot_instance.get_user_settings(user_id)

```
settings_text = f"""
```

📊 **Your Settings**

💹 **Slippage:** {settings[‘slippage’]}%
⚡ **Gas Priority:** {settings[‘gas_priority’].capitalize()}
🛡️ **MEV Protection:** {‘✅’ if settings[‘mev_protection’] else ‘❌’}
✅ **Auto Approve:** {‘✅’ if settings[‘auto_approve’] else ‘❌’}
🔔 **Notifications:** {‘✅’ if settings[‘notifications’] else ‘❌’}

**Auto Trade:**
🎯 **Take Profit:** {settings[‘take_profit’]}%
🛑 **Stop Loss:** {settings[‘stop_loss’]}%
📉 **Trailing Stop:** {‘✅’ if settings[‘trailing_stop’] else ‘❌’}
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back to Settings", callback_data='settings')]]

await query.edit_message_text(
    settings_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def show_auto_trade(query, user_id: str):
“”“Show auto trade settings”””
auto_text = “””
🤖 **Auto Trade Settings**

Configure automated trading strategies:

• 🚀 Auto-buy new token launches
• 🎯 Automatic take profit
• 🛑 Automatic stop loss
• 📉 Trailing stop orders

Automate your trading strategy!
“””

```
await query.edit_message_text(
    auto_text,
    reply_markup=get_auto_trade_keyboard(),
    parse_mode='Markdown'
)
```

async def toggle_auto_buy(query, user_id: str):
“”“Toggle auto buy”””
auto_text = “””
🚀 **Auto Buy New Tokens**

⚠️ Coming soon!

This feature will automatically buy new token launches based on your criteria.

Stay tuned!
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back", callback_data='auto_trade')]]

await query.edit_message_text(
    auto_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def set_take_profit(query, user_id: str):
“”“Set take profit”””
settings = bot_instance.get_user_settings(user_id)
current = settings[‘take_profit’]

```
tp_text = f"""
```

🎯 **Take Profit**

Current: **{current}%**

When your position reaches this profit %, it will automatically sell.

Send a number to set new take profit %:
Example: `50` for 50%
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back", callback_data='auto_trade')]]

await query.edit_message_text(
    tp_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def set_stop_loss(query, user_id: str):
“”“Set stop loss”””
settings = bot_instance.get_user_settings(user_id)
current = settings[‘stop_loss’]

```
sl_text = f"""
```

🛑 **Stop Loss**

Current: **{current}%**

When your position loses this %, it will automatically sell.

Send a number to set new stop loss %:
Example: `25` for 25%
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back", callback_data='auto_trade')]]

await query.edit_message_text(
    sl_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def toggle_trailing_stop(query, user_id: str):
“”“Toggle trailing stop”””
settings = bot_instance.get_user_settings(user_id)
settings[‘trailing_stop’] = not settings[‘trailing_stop’]
bot_instance.save_data()

```
status = "✅ Enabled" if settings['trailing_stop'] else "❌ Disabled"

trailing_text = f"""
```

📉 **Trailing Stop**

Status: **{status}**

Trailing stop locks in profits as price rises, then sells when price drops by a set %.

Percentage: {settings[‘trailing_percent’]}%
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back", callback_data='auto_trade')]]

await query.edit_message_text(
    trailing_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def view_auto_rules(query, user_id: str):
“”“View auto trade rules”””
settings = bot_instance.get_user_settings(user_id)

```
rules_text = f"""
```

📋 **Auto Trade Rules**

🎯 **Take Profit:** {settings[‘take_profit’]}%
🛑 **Stop Loss:** {settings[‘stop_loss’]}%
📉 **Trailing Stop:** {‘✅’ if settings[‘trailing_stop’] else ‘❌’}
└─ Percent: {settings[‘trailing_percent’]}%

**Status:** Active on all positions
“””

```
keyboard = [[InlineKeyboardButton("◀️ Back", callback_data='auto_trade')]]

await query.edit_message_text(
    rules_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def show_analytics(query, user_id: str):
“”“Show analytics”””
positions = bot_instance.user_positions.get(user_id, {})

```
total_trades = len(positions) + 5  # Demo
win_rate = 65.5  # Demo
total_pnl = 234.56  # Demo

analytics_text = f"""
```

📈 **Analytics Dashboard**

**Trading Stats:**
📊 Total Trades: {total_trades}
✅ Win Rate: {win_rate}%
💰 Total P&L: ${total_pnl:+.2f}

**Top Performers:**
🥇 BONK: +125%
🥈 WIF: +89%
🥉 POPCAT: +67%

**This Week:**
📈 Profit: $156.78
📉 Loss: $23.45
💎 Net: +$133.33
“””

```
keyboard = [
    [InlineKeyboardButton("🔄 Refresh", callback_data='analytics')],
    [InlineKeyboardButton("◀️ Main Menu", callback_data='main_menu')]
]

await query.edit_message_text(
    analytics_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

async def show_help(query, user_id: str):
“”“Show help”””
help_text = “””
📚 **Help & Commands**

**Trading:**
/buy <address> - Buy a token
/sell <address> - Sell a token
/positions - View positions

**Wallet:**
/wallet - Manage wallet
/deposit - Deposit info
/withdraw - Withdraw funds

**Settings:**
/settings - Configure bot
/auto - Auto-trade settings

**Info:**
/help - This help message
/start - Restart bot

**Support:**
Join: @trojan_on_solana
“””

```
keyboard = [[InlineKeyboardButton("◀️ Main Menu", callback_data='main_menu')]]

await query.edit_message_text(
    help_text,
    reply_markup=InlineKeyboardMarkup(keyboard),
    parse_mode='Markdown'
)
```

def main():
“”“Main function to run the bot”””
# Get bot token from environment or hardcoded
BOT_TOKEN = os.getenv(‘TELEGRAM_BOT_TOKEN’, ‘YOUR_BOT_TOKEN_HERE’)

```
if BOT_TOKEN == 'YOUR_BOT_TOKEN_HERE':
    print("⚠️  Please set your Telegram bot token!")
    print("Set TELEGRAM_BOT_TOKEN environment variable or edit the code.")
    return

# Create application
application = Application.builder().token(BOT_TOKEN).build()

# Add handlers
application.add_handler(CommandHandler("start", start))
application.add_handler(CommandHandler("help", help_command))
application.add_handler(CommandHandler("wallet", wallet_command))
application.add_handler(CommandHandler("positions", positions_command))
application.add_handler(CommandHandler("buy", buy_command))
application.add_handler(CommandHandler("sell", sell_command))
application.add_handler(CallbackQueryHandler(button_callback))

# Start bot
print("🚀 Trojan Bot starting...")
print("✅ Bot is running!")
application.run_polling(allowed_updates=Update.ALL_TYPES)
```

if **name** == ‘**main**’:
main()