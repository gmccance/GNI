# GNI: General Notification Infrastructure: event based monitoring for CERN

##Infrastructure no more unwanted mails and tickets
##v0.1
### Miguel Santos, Gavin McCance, March 2012

## Aim

Currently the delivery of notifications is done in one of two ways. It is either email based, typically sent from automated scripts, or it is tightly bound to the CC Operator / Sysadmin / Service Manager alarm chain.

The aim of this document is to propose a more flexible and configurable architecture for the delivery of these notifications.

[ Lemon - the current metric and alarm system used at CERN ]

## General Concept

The general concept is that notifications (either “alarms” raised on a node or on a service, or notifications from scripts) will be sent out with a set of configurable tags. We define a number of subscribers to these tagged notification messages. Here, we describe four subscribers, with the possibility that any given notification could be delivered to more than one subscriber:

 * Functional Element (FE) in the CERN ServiceNow (SNOW): the notification is delivered to the relevant FE by SNOW. i.e. a incident is raised in SNOW. Dependent upon the local configuration of the alarm, the incident is can be directly raised to:
 * the Sysadmin team
 * the Hardware Repair Service
 * the Service FE responsible.
 * CC operations dashboard: the notification is delivered to the CC operator - in the way LAS currently functions.
 * Service FE dashboard: the notification is delivered to a service dashboard which is used by the service FE responsible to view all current notifications, alarms, and warnings on their systems. It is expected that all notifications relevant for a service will be delivered to the Service FE dashboard.
 * SMS service: the notification is delivered to a service which sends an SMS to a configured set of numbers
 * Email service: the notification is delivered to a service which sends an email.

As necessary, a copy of all messages can be delivered to a general notification logging service for historical storage and analysis, or other interested subscribers.

It is expected that a given “notification” (whether a Lemon alarm, or a automated script) will be provided with a “standard” set of notification targets. For example, a broken disk alarm should be delivered to the Hardware Repair Service, via SNOW, whereas, a `random_daemon_wrong` alarm might reasonably be delivered directly to the service manager FE via SNOW.

Additionally, for a particularly critical service, the notification might be also configured to be delivered to the Operator dashboard or even the SMS service.
￼
## Example binding to lemon agent

For each Lemon alarm, there should be a configurable set of “tags” which define the default destination(s). Service managers should be able to override the configuration for their service, to change or add destinations, as necessary.

The lemon agent needs to be enhanced to additionally send the “exception” to the interested subscribers.
It is expected that if Lemon alarms can be delivered appropriately, this should result in a significant reduction in load on the CC operators - alarms for which they add no extra value should not be seen by them.

## Example binding to ‘my favourite script’

Instead of sending an email to the “service responsible”, your favourite script should send a tagged message which will result in the delivery of the message to the relevant subscribers. Ideally, there would be a default recipient set, which could be overridden per FE.

For example, the message sent for “cluster” changes would be tagged such that they ended up as an email and an item on the FE dashboard. Some services, with very large numbers of hosts, might prefer to only receive the notifications via the FE dashboard.

For example, the security scan noting suspicious use of one of your nodes would be delivered to the FE dashboard and to the FE via a SNOW incident.

## Conclusion

The proposal decouples the delivery of “notifications” from any underlying system.

It allows for much more flexible specification of the desired destination(s) of a notification or an alarm. It also provides a framework for new types of (non-Lemon) notifications to be sent.

By sending the notifications and alarms to the right place, it should make incident response faster and more efficient, and offload the CC operations team from having to handle alarms that they add no value to.

Rather critical to the proposal is the creation of service FE dashboards which allow a service responsible to view all notifications that have been raised against their service.
It is expected that the system would make use of a messaging infrastructure as transport for the notifications.

