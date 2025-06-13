# types.ts

## Overview

This file (`types.ts`) defines the core TypeScript data structures, interfaces, and enums for the Agent-to-Agent (A2A) communication protocol. These type definitions are essential for ensuring type safety and consistency in TypeScript-based client or server implementations of the A2A protocol. They mirror the structures defined in the JSON schema and gRPC protocol buffer definitions, providing a clear and typed way to work with A2A objects and messages.

## Key Components

This file exports numerous TypeScript interfaces, enums, and type aliases. Below are some of the most important ones:

### AgentCard
(Interface)
Defines the structure of an agent's capability card, including its name, description, URL, skills, security schemes, and supported communication modes.
- `name: string`
- `description: string`
- `url: string`
- `skills: AgentSkill[]`
- `capabilities: AgentCapabilities`
- `securitySchemes?: { [scheme: string]: SecurityScheme }`
- `defaultInputModes: string[]`
- `defaultOutputModes: string[]`

### AgentSkill
(Interface)
Describes a specific capability or skill of an agent.
- `id: string`
- `name: string`
- `description: string`
- `tags: string[]`
- `examples?: string[]` (example prompts)
- `inputModes?: string[]`
- `outputModes?: string[]`

### Task
(Interface)
Represents a task being processed by an agent.
- `id: string` (Unique identifier for the task)
- `contextId: string` (Identifier for the context)
- `status: TaskStatus` (Current status of the task)
- `history?: Message[]` (Interaction history)
- `artifacts?: Artifact[]` (Output artifacts)
- `kind: "task"` (Discriminator property)

### TaskState
(Enum)
Defines the possible states a `Task` can be in.
- `Submitted = "submitted"`
- `Working = "working"`
- `InputRequired = "input-required"`
- `Completed = "completed"`
- `Canceled = "canceled"`
- `Failed = "failed"`
- `Rejected = "rejected"`
- `AuthRequired = "auth-required"`
- `Unknown = "unknown"`

### Message
(Interface)
Represents a message exchanged between a user and an agent.
- `role: "user" | "agent"` (Sender's role)
- `parts: Part[]` (Content of the message)
- `messageId: string` (Unique message identifier)
- `taskId?: string` (Associated task ID)
- `contextId?: string` (Associated context ID)
- `kind: "message"` (Discriminator property)

### Part
(Type Alias)
A union type representing the different kinds of content parts in a `Message` or `Artifact`.
- `type Part = TextPart | FilePart | DataPart;`

### TextPart
(Interface)
A part of a message that contains plain text.
- `kind: "text"`
- `text: string`

### FilePart
(Interface)
A part of a message that represents a file, which can be specified by URI or by bytes.
- `kind: "file"`
- `file: FileWithBytes | FileWithUri` (where `FileWithBytes` has `bytes: string` and `FileWithUri` has `uri: string`)

### DataPart
(Interface)
A part of a message that contains structured data (generic JSON object).
- `kind: "data"`
- `data: { [key: string]: any; }`

### JSONRPCRequest
(Interface)
Base interface for JSON-RPC 2.0 request objects.
- `jsonrpc: "2.0"`
- `id?: number | string | null`
- `method: string`
- `params?: { [key: string]: any }`

### JSONRPCResponse
(Type Alias)
A union type for various specific JSON-RPC response types (e.g., `SendMessageResponse`, `GetTaskResponse`).

### A2ARequest
(Type Alias)
A union type encompassing all specific A2A request types (e.g., `SendMessageRequest`, `GetTaskRequest`).

### A2AError
(Type Alias)
A union type for all A2A specific error interfaces (e.g., `TaskNotFoundError`, `InvalidParamsError`). Each error interface extends `JSONRPCError` with a specific `code` and `message`.

### SecurityScheme
(Type Alias)
A union type for different security scheme configurations (e.g., `APIKeySecurityScheme`, `OAuth2SecurityScheme`).

## Important Variables/Constants

N/A (This file primarily defines TypeScript types (interfaces, enums, type aliases) and does not export runtime constants or variables.)

## Usage Examples

Here's how these types might be imported and used in a TypeScript application:

```typescript
import { AgentCard, Message, Task, TaskState, TextPart } from './types';

// Example of defining an AgentCard variable
const myAgentCard: AgentCard = {
  name: "Documentation Agent",
  description: "An agent that helps write documentation.",
  url: "http://example.com/agent",
  version: "1.0.1",
  capabilities: { streaming: true },
  defaultInputModes: ["text/plain"],
  defaultOutputModes: ["text/markdown"],
  skills: [
    {
      id: "doc-writer",
      name: "Document Writer",
      description: "Writes documents based on specifications.",
      tags: ["documentation", "writing"],
      examples: ["Write a document about types.ts"],
    }
  ]
};

// Example of creating a message
const userMessage: Message = {
  role: "user",
  messageId: "msg-001",
  contextId: "ctx-789",
  kind: "message",
  parts: [
    {
      kind: "text",
      text: "Hello, agent!"
    } as TextPart // Type assertion might be needed for union types
  ]
};

// Example of using an enum for task state
function processTask(task: Task): void {
  if (task.status.state === TaskState.Submitted) {
    console.log(`Task ${task.id} is submitted.`);
    // Further processing
  }
}
```

## Dependencies and Interactions

- **TypeScript**: These definitions are written in TypeScript and are intended for use in TypeScript projects. They leverage TypeScript features like interfaces, enums, type aliases, and discriminated unions (`kind` property).
- **JSON Schema / Protobuf**: The types defined here are designed to align closely with the structures defined in the A2A JSON schema (`a2a.json`) and the gRPC protocol buffer definitions (`a2a.proto`). This ensures consistency across different representations of the A2A protocol.
- **A2A Client/Server Implementations**: Any TypeScript-based A2A client or server application would import and use these types extensively to define the shape of data being exchanged, for function signatures, and for ensuring type safety at compile time.
- **No External Libraries**: The `types.ts` file, as presented, appears to be self-contained and does not import types from external libraries, relying only on standard TypeScript features.
