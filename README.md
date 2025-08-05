# Interactive Brokers MCP Server

A Model Context Protocol (MCP) server that connects to Interactive Brokers API for comprehensive trading operations, market data retrieval, and portfolio management.

## Features

- **Real-time Market Data**: Get live quotes, bid/ask prices, and market data
- **Historical Data**: Retrieve historical price data with customizable time frames
- **Portfolio Management**: View positions, account summary, and portfolio metrics
- **Order Management**: Place, modify, and cancel orders
- **Account Information**: Access account details, buying power, and cash balances
- **Multi-Asset Support**: Stocks, options, futures, forex, and more
- **Connection Management**: Automatic connection handling with TWS/IB Gateway

## Available Tools

### Connection Management
- `connect_to_ibkr()` - Manually establish connection to IBKR
- `get_connection_status()` - Check current connection status

### Account & Portfolio
- `get_account_summary()` - Get account summary with cash, buying power, etc.
- `get_positions()` - Retrieve all current positions
- `calculate_portfolio_metrics()` - Calculate portfolio metrics and P&L

### Market Data
- `get_market_data(symbol, exchange, sec_type)` - Get real-time market data
- `get_historical_data(symbol, duration, bar_size, what_to_show, exchange, sec_type)` - Retrieve historical data

### Order Management
- `place_order(symbol, action, quantity, order_type, limit_price, exchange, sec_type)` - Place trading orders
- `get_open_orders()` - Get all open/pending orders
- `get_all_orders()` - Get all orders (completed and pending)
- `get_order_status(order_id)` - Check status of specific order
- `cancel_order(order_id)` - Cancel an existing order

## Prerequisites

### Interactive Brokers Account
- Active Interactive Brokers account
- TWS (Trader Workstation) or IB Gateway installed and running
- API access enabled in TWS/Gateway settings

### TWS/IB Gateway Setup
1. **Download and install** TWS or IB Gateway from Interactive Brokers
2. **Enable API access**:
   - In TWS: Go to File → Global Configuration → API → Settings
   - Check "Enable ActiveX and Socket Clients" --only for Windows
   - Add trusted IP addresses (127.0.0.1 for local use)
3. **Configure ports**:
   - IB Gateway: Default port 4001
   - TWS Paper Trading: Default port 7497
   - TWS Live Trading: Default port 7496

## Installation

### Using Docker (Recommended)

```bash
docker pull gpolydatas/ibkr-mcp-server:latest
docker run -d --name ibkr-mcp-server \
  -e IBKR_HOST=127.0.0.1 \
  -e IBKR_PORT=4001 \
  -e IBKR_CLIENT_ID=0 \
  --network host \
  gpolydatas/ibkr-mcp-server:latest
```

### From Source

```bash
git clone https://github.com/gpolydatas/ibkr-mcp-server.git
cd ibkr-mcp-server
pip install -r requirements.txt
python ibkr_mcp_server.py
```

## Configuration

### Environment Variables

| Variable | Description | Default | Example |
|----------|-------------|---------|---------|
| `IBKR_HOST` | TWS/Gateway host address | `127.0.0.1` | `127.0.0.1` |
| `IBKR_PORT` | TWS/Gateway port | `4001` | `4001` (Gateway), `7497` (TWS Paper) |
| `IBKR_CLIENT_ID` | Client ID for connection | `0` | `0` |

### Port Reference

| Service | Port | Description |
|---------|------|-------------|
| IB Gateway | 4001 | Live/Paper trading via Gateway |
| TWS Paper Trading | 7497 | Paper trading via TWS |
| TWS Live Trading | 7496 | Live trading via TWS |

## Usage Examples

### Basic Connection Test
```python
# Test connection to IBKR
result = await connect_to_ibkr()
print(result)
```

### Get Account Information
```python
# Get account summary
account = await get_account_summary()
print(f"Buying Power: {account['account_info']['AvailableFunds']['value']}")

# Get current positions
positions = await get_positions()
for symbol, position in positions['positions'].items():
    print(f"{symbol}: {position['position']} shares")
```

### Market Data
```python
# Get real-time market data for AAPL
market_data = await get_market_data("AAPL", "SMART", "STK")
print(f"AAPL Price: {market_data['market_data'].get('LAST', 'N/A')}")

# Get historical data
historical = await get_historical_data("AAPL", "1 D", "1 min", "TRADES")
print(f"Retrieved {historical['bars_count']} bars")
```

### Trading
```python
# Place a market order to buy 100 shares of AAPL
order = await place_order("AAPL", "BUY", 100, "MKT")
print(f"Order ID: {order['order_id']}")

# Check order status
status = await get_order_status(order['order_id'])
print(f"Order Status: {status['order_info']['status']}")

# Get all open orders
open_orders = await get_open_orders()
print(f"Open Orders: {open_orders['total_open_orders']}")
```

## API Reference

### Order Types
- `MKT` - Market Order
- `LMT` - Limit Order
- `STP` - Stop Order
- `STP LMT` - Stop Limit Order

### Security Types
- `STK` - Stock
- `OPT` - Option
- `FUT` - Future
- `CASH` - Forex
- `IND` - Index

### Historical Data Parameters
- **Duration**: `1 D`, `1 W`, `1 M`, `1 Y`, etc.
- **Bar Size**: `1 min`, `5 mins`, `15 mins`, `1 hour`, `1 day`, etc.
- **What to Show**: `TRADES`, `MIDPOINT`, `BID`, `ASK`, `BID_ASK`, etc.

## Error Handling

The server includes comprehensive error handling for common issues:

- **Connection failures**: Automatic retry logic
- **Market data limitations**: Graceful handling of data subscription limits
- **Order validation**: Pre-submission order validation
- **Network timeouts**: Configurable timeout handling

## Security Considerations

- **Local Network**: Designed for local network use only
- **API Permissions**: Requires proper IBKR API permissions
- **Data Protection**: No sensitive data stored permanently
- **Connection Security**: Uses IBKR's secure API protocols

## Troubleshooting

### Common Issues

**Connection Failed**
```
Failed to connect to IBKR. Make sure TWS or IB Gateway is running and API connections are enabled.
```
- Ensure TWS/Gateway is running
- Check API settings are enabled
- Verify port number and host address
- Check firewall settings

**Permission Denied**
- Verify IBKR account has API trading permissions
- Check if API access is enabled in account settings
- Ensure client ID is unique

**Market Data Issues**
- Verify market data subscriptions in IBKR account
- Check if market is open for requested symbols
- Some data requires additional permissions

### Debug Mode

Set log level to DEBUG for detailed connection information:
```python
logging.basicConfig(level=logging.DEBUG)
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details.

## Disclaimer

**Important**: This software is for educational and development purposes. Trading involves risk of financial loss. Always test thoroughly with paper trading before using with live accounts. The authors are not responsible for any financial losses incurred through the use of this software.

## Support

- **Issues**: Report bugs and feature requests on [GitHub Issues](https://github.com/gpolydatas/ibkr-mcp-server/issues)
- **Documentation**: [Interactive Brokers API Documentation](https://interactivebrokers.github.io/tws-api/)
- **Community**: Join discussions in the repository discussions section

## Acknowledgments

- Built with [FastMCP](https://github.com/pydantic/fastmcp)
- Uses [Interactive Brokers Python API](https://github.com/InteractiveBrokers/tws-api)
- Follows [Model Context Protocol](https://modelcontextprotocol.io/) specification
