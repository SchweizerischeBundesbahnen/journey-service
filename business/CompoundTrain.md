# CompoundTrain
(de:Zugformation) may be requested for `PTRideLeg` or `DatedVehicleJourney` by request parameter `includeTrainStopAssignment`, Request a desired `Trip` by /v3/trips/{id} &includeTrainStopAssignment.

Returns details about train-compositions (aka formation) their wagons, sections and Occupancy at origin and destination of a PTRideLeg.

Be aware:
* CompoundTrain's are considered realtime data, therefore they are only available today!