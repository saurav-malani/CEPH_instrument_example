# OpenTracing Jaeger Examples

This is a simple application that consist of two services & and they are deployed to Kubernetes and is instrumented using the Opentracing API. As the Opentracing instrumentation is actually going to be used to capture backend tracing information which is reported to Jaeger. But, for the sake of completion we have also used Prometheus to get application metrics. 

The following are the different components that will be shown in Kubernetes dashboard:-
1. The application has two services the account manager and the order manager.
2. Jaeger Open-tracing backend.
3. Metrics will be shown in Grafana.
4. Prometheus Operator is used to capture the metrics.


The Order manager: It is a simple spring boot rest application.


The methods in OrderMgrController shows the rest end point  of the "buy", "sell", "fail", "health". These will basically have a random delay in them
just to make some of the matrix more interesting.

key idea:
In each of these a line is added to access the open tracing tracer, to set what is called a baggage item.
Baggage is an Open Tracing concept that was used to allow application to carry additional application specific 
information along with the the trace context to any subsequent services called in the chain. So, here we are 
are setting the baggage item to transaction. So, we're using it to let other services in the chain of the server
down the invocation chain know in what business context there services has been called. 

So, here we have set transaction to "buy", "sell", "fail", "health". All of the methods call the accounts manager, the only
differences with the sell is that it uses an incorrect URL so it is going to fail and so it'll introduce an error info into the tracing. 

The other couple of classes in other services are to do with configuraion of Prometheus and Opentracing. So, the Prometheus one identifies which end point the prometheus should get the matrix from and then the opentracing. method "io.opentracing.Tracker" would return the tracer. The opentracing traces are resolved using "io.opentracing.contrib.tracerresolver.TracerResolver.resolveTracer()" mechanism, tracer resolver. So, that would load the opentracing client Jaeger. And then I added decorator mechanism that decorates the tracer with some metrics capabilities and newMetricsReporter is used to establish the reporter, from which we know which adapter we're going to use
to send the metric info. We are also setting an additional custom label in the above method, based on baggage. So, what we are doing is we are saying all the metrics should be reported with a big additional baggage lebel and the label and the label it should use is transaction, if it can't find a value, then use it "n/a" as default. So, this means is, what we can do is, we can capture the service application metrics but they'll be capegorized based on the business transcation in which they're being performed and then the other
 thing to do is configuration, because prometheus is going to be collecting the metrics on the metrics endpoint. So, we want to tell the tracer to ignore that endpoint otherwise would be continually recording tracing information related to that end point as well.  

Account service is very similar to the above to the service. The only difference is the resting point that is presenting. And rest as similar to above, like simple delays and error in case of account not found. 






First step is to install the OpenTracing compliant tracing system and Prometheus in the cloud environment.

* [Kubernetes instructions](Kubernetes.md)

* [OpenShift instructions](OpenShift.md)

* [Istio/Kubernetes instructions](Istio-Kubernetes.md)

The second step is to try out the example. Instructions are provided in the sub-folder on how to deploy
and use the example.


