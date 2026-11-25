# PWX Agent Guard for Cursor

Security for your GPU, TPU and CPU, all the way down.

AI agents now run commands that reach real hardware: power caps, clocks, firmware, BMC resets, power cycles, GPU allocation. PWX Agent Guard gives the agent a check before it acts. The agent asks PWX, PWX answers **allow**, **hold** or **deny** with the layer of the stack the action touches, and the action is recorded as a chain from agent to command to silicon.

By [BareketAI](https://pwx.bareket.ai) (PWX).

## What the agent gets

| Verdict | Examples |
|---|---|
| deny | flash firmware, set microcode, disable CPU mitigations (irreversible or side-channel risk) |
| hold | power cap, clock change, BMC reset, power cycle, GPU allocation (needs human review) |
| allow | read posture, add a label |

## Tools

| Tool | What it does |
|---|---|
| `assess_infra_action` | Before a hardware action: returns allow / hold / deny, the stack floor it touches (compute, device, firmware, board, physical), a reason and a severity. |
| `attest_action` | After the action: records who (agent), what (command), where (chip or node), the verdict and the observed effect. Returns a correlation id for the audit trail. |
| `get_compute_posture` | Security posture for a GPU, TPU or CPU, one status per floor of the stack. |
| `get_gpu_power_posture` | Per-GPU power cap, draw and throttle state (DCGM). |
| `list_power_anomalies` | Recent power-layer anomalies: mining patterns, power-cap tampering, undervolt, throttling. |
| `get_rack_power_telemetry` | Watts per rack, node and GPU. |

## Install

Install **PWX Agent Guard** from the Cursor Marketplace. The plugin adds one remote MCP server:

```json
{
  "mcpServers": {
    "pwx-agent-guard": { "url": "https://mcp.bareket.ai" }
  }
}
```

No API key and no configuration are needed for the public endpoint.

## Try it

Ask the agent in Cursor, for example:

- "Before you change the power limit on GPU 0, check it with PWX."
- "Is flashing the BMC firmware on node-07 safe?"
- "Show the security posture of this GPU down the stack."

Or call the server directly:

```sh
curl -s https://mcp.bareket.ai -X POST -H 'content-type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"assess_infra_action","arguments":{"action":"flash_firmware","target":{"chip":"gpu"}}}}'
```

## Important: demo endpoint

`https://mcp.bareket.ai` is a public demo. It is **not connected to your infrastructure**. Verdicts come from the PWX policy model, and the telemetry, posture and anomaly tools return data from the PWX lab. To connect Agent Guard to your own fleet (PWX Edge, DCGM, Redfish), contact [se@bareket.ai](mailto:se@bareket.ai) or see [pwx.bareket.ai](https://pwx.bareket.ai).

## Privacy

The plugin sends the tool arguments your agent passes (for example an action name and a target chip) to `mcp.bareket.ai`. Do not pass secrets, credentials or customer data in tool arguments.

## License

MIT. See [LICENSE](LICENSE).
