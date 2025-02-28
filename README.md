**Reproducer:**
```bash
ATTRIBUTES=(
jvm.memory.used
jvm.memory.committed
jvm.memory.limit
jvm.memory.used_after_last_gc
jvm.gc.duration
jvm.thread.count
jvm.class.loaded
jvm.class.unloaded
jvm.class.count
jvm.cpu.time
jvm.cpu.count
jvm.cpu.recent_utilization
)

mvn clean package -DskipTests

for ATTRIBUTE in ${ATTRIBUTES[*]}
do
  echo "===========  $ATTRIBUTE  ==========="
  java -jar target/quarkus-app/quarkus-run.jar | grep "$ATTRIBUTE," &
  for i in {1..500}; do curl -s http://localhost:8080/hello > /dev/null ; sleep 0.001; done
  sleep 10
  PID=`jps -l | grep target/quarkus-app/quarkus-run.jar | cut -d" " -f 1`
  kill $PID
  echo ""
done
```

**Notes:**

jvm.cpu.recent_utilization ... same?
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.cpu.recent_utilization, description=Recent CPU utilization for the process as reported by the JVM., unit=1, type=DOUBLE_GAUGE, data=ImmutableGaugeData{points=[ImmutableDoublePointData{startEpochNanos=1740577225142481000, epochNanos=1740577235148171000, attributes={}, value=0.0, exemplars=[]}]}}
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java17, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.cpu.recent_utilization, description=Recent CPU utilization for the process as reported by the JVM., unit=1, type=DOUBLE_GAUGE, data=ImmutableGaugeData{points=[ImmutableDoublePointData{startEpochNanos=1740577225142481000, epochNanos=1740577235148171000, attributes={}, value=0.0, exemplars=[]}]}}
```
jvm.cpu.count   -   io.opentelemetry.runtime-telemetry-java8

jvm.cpu.time    -   io.opentelemetry.runtime-telemetry-java8

jvm.class.count ... same?
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.class.count, description=Number of classes currently loaded., unit={class}, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577179361410000, epochNanos=1740577189368283000, attributes={}, value=6535, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java17, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.class.count, description=Number of classes currently loaded., unit={class}, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577179361410000, epochNanos=1740577189368283000, attributes={}, value=6525, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
```

jvm.class.unloaded ... same?
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.class.unloaded, description=Number of classes unloaded since JVM start., unit={class}, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577164245683000, epochNanos=1740577174252825000, attributes={}, value=0, exemplars=[]}], monotonic=true, aggregationTemporality=CUMULATIVE}}
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java17, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.class.unloaded, description=Number of classes unloaded since JVM start., unit={class}, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577164245683000, epochNanos=1740577174252825000, attributes={}, value=0, exemplars=[]}], monotonic=true, aggregationTemporality=CUMULATIVE}}
```

jvm.class.loaded ... same?
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.class.loaded, description=Number of classes loaded since JVM start., unit={class}, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577149056550000, epochNanos=1740577159063599000, attributes={}, value=6555, exemplars=[]}], monotonic=true, aggregationTemporality=CUMULATIVE}}
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java17, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.class.loaded, description=Number of classes loaded since JVM start., unit={class}, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577149056550000, epochNanos=1740577159063599000, attributes={}, value=6528, exemplars=[]}], monotonic=true, aggregationTemporality=CUMULATIVE}}
```

jvm.thread.count  .... Number of executing threads vs. Number of executing platform threads.
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.thread.count, description=Number of executing platform threads., unit={thread}, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577133916770000, epochNanos=1740577143924240000, attributes={jvm.thread.daemon=true, jvm.thread.state="waiting"}, value=3, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577133916770000, epochNanos=1740577143924240000, attributes={jvm.thread.daemon=false, jvm.thread.state="waiting"}, value=1, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577133916770000, epochNanos=1740577143924240000, attributes={jvm.thread.daemon=false, jvm.thread.state="runnable"}, value=13, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577133916770000, epochNanos=1740577143924240000, attributes={jvm.thread.daemon=true, jvm.thread.state="timed_waiting"}, value=9, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577133916770000, epochNanos=1740577143924240000, attributes={jvm.thread.daemon=true, jvm.thread.state="runnable"}, value=5, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java17, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.thread.count, description=Number of executing threads, unit={thread}, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577133916770000, epochNanos=1740577143924240000, attributes={jvm.thread.daemon=true}, value=17, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577133916770000, epochNanos=1740577143924240000, attributes={jvm.thread.daemon=false}, value=14, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
```

jvm.gc.duration
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.gc.duration, description=Duration of JVM garbage collection actions., unit=s, type=HISTOGRAM, data=ImmutableHistogramData{aggregationTemporality=CUMULATIVE, points=[ImmutableHistogramPointData{getStartEpochNanos=1740577118867614000, getEpochNanos=1740577128875893000, getAttributes={jvm.gc.action="end of minor GC", jvm.gc.name="G1 Young Generation"}, getSum=0.002, getCount=2, hasMin=true, getMin=0.001, hasMax=true, getMax=0.001, getBoundaries=[0.01, 0.1, 1.0, 10.0], getCounts=[2, 0, 0, 0, 0], getExemplars=[]}, ImmutableHistogramPointData{getStartEpochNanos=1740577118867614000, getEpochNanos=1740577128875893000, getAttributes={jvm.gc.action="end of concurrent GC pause", jvm.gc.name="G1 Concurrent GC"}, getSum=0.001, getCount=2, hasMin=true, getMin=0.0, hasMax=true, getMax=0.001, getBoundaries=[0.01, 0.1, 1.0, 10.0], getCounts=[2, 0, 0, 0, 0], getExemplars=[]}]}}
```

jvm.memory.used_after_last_gc   ???? attributes={jvm.memory.pool.name="G1 Survivor Space", jvm.memory.type="heap"}, value=0
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.memory.used_after_last_gc, description=Measure of memory used, as measured after the most recent garbage collection event on this pool., unit=By, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577103728433000, epochNanos=1740577113738294000, attributes={jvm.memory.pool.name="G1 Eden Space", jvm.memory.type="heap"}, value=0, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577103728433000, epochNanos=1740577113738294000, attributes={jvm.memory.pool.name="G1 Old Gen", jvm.memory.type="heap"}, value=1081264, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577103728433000, epochNanos=1740577113738294000, attributes={jvm.memory.pool.name="G1 Survivor Space", jvm.memory.type="heap"}, value=8822528, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java17, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.memory.used_after_last_gc, description=Measure of memory used, as measured after the most recent garbage collection event on this pool., unit=By, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577103728433000, epochNanos=1740577113738294000, attributes={jvm.memory.pool.name="G1 Eden Space", jvm.memory.type="heap"}, value=0, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577103728433000, epochNanos=1740577113738294000, attributes={jvm.memory.pool.name="G1 Survivor Space", jvm.memory.type="heap"}, value=0, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
```

jvm.memory.limit    ???  "Compressed Class Space", jvm.memory.type="non_heap"}, value=0  ....  Metaspace nula ?
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.memory.limit, description=Measure of max obtainable memory., unit=By, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577087938028000, epochNanos=1740577097952296000, attributes={jvm.memory.pool.name="Compressed Class Space", jvm.memory.type="non_heap"}, value=1073741824, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577087938028000, epochNanos=1740577097952296000, attributes={jvm.memory.pool.name="CodeHeap 'non-nmethods'", jvm.memory.type="non_heap"}, value=5849088, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577087938028000, epochNanos=1740577097952296000, attributes={jvm.memory.pool.name="CodeHeap 'profiled nmethods'", jvm.memory.type="non_heap"}, value=122896384, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577087938028000, epochNanos=1740577097952296000, attributes={jvm.memory.pool.name="G1 Old Gen", jvm.memory.type="heap"}, value=9663676416, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577087938028000, epochNanos=1740577097952296000, attributes={jvm.memory.pool.name="CodeHeap 'non-profiled nmethods'", jvm.memory.type="non_heap"}, value=122912768, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java17, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.memory.limit, description=Measure of max obtainable memory., unit=By, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577087938028000, epochNanos=1740577097952296000, attributes={jvm.memory.pool.name="Compressed Class Space", jvm.memory.type="non_heap"}, value=0, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577087938028000, epochNanos=1740577097952296000, attributes={jvm.memory.pool.name="Metaspace", jvm.memory.type="non_heap"}, value=0, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
```

jvm.memory.committed    ???   Compressed Class Space", jvm.memory.type="non_heap"}, value=0  "G1 Eden Space" + "Metaspace" ... all 0 for java17
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.memory.committed, description=Measure of memory committed., unit=By, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="Compressed Class Space", jvm.memory.type="non_heap"}, value=3866624, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="CodeHeap 'non-nmethods'", jvm.memory.type="non_heap"}, value=2555904, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="Metaspace", jvm.memory.type="non_heap"}, value=30146560, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="G1 Survivor Space", jvm.memory.type="heap"}, value=16777216, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="CodeHeap 'profiled nmethods'", jvm.memory.type="non_heap"}, value=10354688, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="G1 Old Gen", jvm.memory.type="heap"}, value=8388608, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="CodeHeap 'non-profiled nmethods'", jvm.memory.type="non_heap"}, value=2555904, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="G1 Eden Space", jvm.memory.type="heap"}, value=58720256, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java17, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.memory.committed, description=Measure of memory committed., unit=By, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="Compressed Class Space", jvm.memory.type="non_heap"}, value=0, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="Metaspace", jvm.memory.type="non_heap"}, value=0, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577072808914000, epochNanos=1740577082815647000, attributes={jvm.memory.pool.name="G1 Eden Space", jvm.memory.type="heap"}, value=0, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
```
jvm.memory.used    ???   Compressed Class Space", jvm.memory.type="non_heap"}, value=0  "G1 Eden Space" + "Metaspace" ... all 0 for java17
```
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java8, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.memory.used, description=Measure of memory used., unit=By, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="Compressed Class Space", jvm.memory.type="non_heap"}, value=3620392, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="CodeHeap 'non-nmethods'", jvm.memory.type="non_heap"}, value=1542144, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="G1 Old Gen", jvm.memory.type="heap"}, value=1081264, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="G1 Survivor Space", jvm.memory.type="heap"}, value=9250432, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="Metaspace", jvm.memory.type="non_heap"}, value=29602192, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="CodeHeap 'profiled nmethods'", jvm.memory.type="non_heap"}, value=10255872, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="G1 Eden Space", jvm.memory.type="heap"}, value=25165824, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="CodeHeap 'non-profiled nmethods'", jvm.memory.type="non_heap"}, value=1649408, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
InstrumentationScopeInfo{name=io.opentelemetry.runtime-telemetry-java17, version=2.8.0-alpha, schemaUrl=null, attributes={}}, name=jvm.memory.used, description=Measure of memory used., unit=By, type=LONG_SUM, data=ImmutableSumData{points=[ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="Compressed Class Space", jvm.memory.type="non_heap"}, value=0, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="G1 Survivor Space", jvm.memory.type="heap"}, value=0, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="Metaspace", jvm.memory.type="non_heap"}, value=0, exemplars=[]}, ImmutableLongPointData{startEpochNanos=1740577057623300000, epochNanos=1740577067630360000, attributes={jvm.memory.pool.name="G1 Eden Space", jvm.memory.type="heap"}, value=0, exemplars=[]}], monotonic=false, aggregationTemporality=CUMULATIVE}}
```
