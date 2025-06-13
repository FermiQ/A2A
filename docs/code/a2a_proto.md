# a2a.proto

## Overview

This file defines the gRPC service protocol for Agent-to-Agent (A2A) communication. It outlines the structure of messages and services that enable agents to interact, send tasks, and exchange information. This protocol is central to the A2A system, providing a standardized way for different agents to communicate regardless of their underlying implementation.

## Key Components

This section lists the primary messages and services defined in `a2a.proto`.

### A2AService

The main gRPC service that defines the available RPC methods for A2A communication.
- `SendMessage`: Sends a message to an agent and returns a task, or a Long-Running Operation (LRO) if requested.
- `SendStreamingMessage`: Sends a message and returns a stream of task update events.
- `GetTask`: Retrieves the current state of a task.
- `CancelTask`: Attempts to cancel an ongoing task.
- `TaskSubscription`: Subscribes to an existing task for a stream of update events.
- `CreateTaskPushNotification`: Sets up a push notification configuration for a task.
- `GetTaskPushNotification`: Retrieves a push notification configuration for a task.
- `ListTaskPushNotification`: Lists all push notification configurations for a task.
- `GetAgentCard`: Retrieves the agent's capability card.

### Message

Represents a unit of communication between a client and an agent. It includes:
- `message_id`: Unique identifier for the message.
- `context_id`: Identifier for the conversational context.
- `task_id`: Identifier for the associated task, if any.
- `role`: Indicates whether the message is from the `USER` or `AGENT`.
- `content`: The actual content of the message, composed of one or more `Part` objects.
- `metadata`: Optional key-value metadata.
- `extensions`: URIs of extensions used in the message.

### Task

Represents the core unit of action in the A2A system. It includes:
- `id`: Unique identifier for the task.
- `context_id`: Identifier for the conversational context this task belongs to.
- `status`: The current `TaskStatus` of the task (e.g., SUBMITTED, WORKING, COMPLETED, FAILED).
- `artifacts`: A list of `Artifact` objects produced by the task.
- `history`: A list of `Message` objects representing the interaction history of the task.
- `metadata`: Optional key-value metadata.

### AgentCardRequest

Request message for `GetAgentCard` RPC. (Currently empty, added for linter compliance).

### AgentCard

Contains information about an agent's capabilities, including:
- `name`: Human-readable name of the agent.
- `description`: Description of the agent's domain.
- `url`: Base URL for interacting with the agent.
- `provider`: Information about the agent's provider.
- `version`: Agent's version.
- `capabilities`: Supported features like streaming and push notifications.
- `security_schemes`: Authentication methods supported by the agent.
- `default_input_modes`: Default supported input MIME types.
- `default_output_modes`: Default supported output MIME types.
- `skills`: A list of `AgentSkill` objects detailing specific capabilities.

### TaskRequest (Conceptual)

While not a single message named `TaskRequest`, various request messages are used for task-related operations:
- `SendMessageRequest`: Used to send a message that may initiate a task. Contains a `Message` and `SendMessageConfiguration`.
- `GetTaskRequest`: Used by `GetTask` to retrieve a task by its name (ID).
- `CancelTaskRequest`: Used by `CancelTask` to request cancellation of a task by its name.
- `TaskSubscriptionRequest`: Used by `TaskSubscription` to subscribe to updates for a task by its name.

## Important Variables/Constants

N/A (Protocol buffer definitions focus on message structures and service definitions, not standalone variables or constants in the traditional code sense.)

## Usage Examples

A client would typically use a gRPC-generated stub for `A2AService` to interact with an agent. For example, to send a message:

```protobuf
// This is a conceptual example of how a client might use the generated code.
// Actual syntax depends on the target language (e.g., Python, Java, Go).

// 1. Create a gRPC channel to the agent's server.
// 2. Create a stub for A2AService using the channel.
// 3. Construct a SendMessageRequest:
//    SendMessageRequest {
//      request: Message {
//        message_id: "client-msg-123",
//        role: ROLE_USER,
//        content: [ Part { text: "Hello Agent, what can you do?" } ]
//      },
//      configuration: SendMessageConfiguration {
//        blocking: false
//      }
//    }
// 4. Call the SendMessage RPC method on the stub with the request.
// 5. Process the SendMessageResponse, which might contain a Task or a Message.
```

To get an agent's capabilities:
```protobuf
// 1. Create a gRPC channel and A2AService stub.
// 2. Construct a GetAgentCardRequest (currently empty).
// 3. Call the GetAgentCard RPC method.
// 4. Process the AgentCard response to understand the agent's skills, auth, etc.
```

## Dependencies and Interactions

- **protobuf**: This file is written in Protocol Buffers language (syntax = "proto3"). It needs to be compiled by `protoc` (the protobuf compiler) to generate client and server code in various programming languages (e.g., Python, Java, Go, C#).
- **google/api/annotations.proto, google/api/client.proto, google/api/field_behavior.proto, google/protobuf/struct.proto, google/protobuf/timestamp.proto**: These are imported Google API and Protobuf well-known type definitions, used for features like HTTP annotations, field behaviors, and standard data types.
- **gRPC Client/Server Implementations**: The compiled output of this `.proto` file is used by gRPC client applications to make requests and by gRPC server applications (the agents) to implement the `A2AService` interface and handle incoming requests.
- **AIP Compliance**: The service design attempts to conform to Google's AIP (API Improvement Proposals) standards, particularly AIP-127 for resource-oriented services.
