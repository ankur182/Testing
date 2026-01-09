static Stream<Arguments> invalidEmploymentRequests() {
    return Stream.of(
        Arguments.of(
            "employment-missing-currency-request.json",
            "employment-missing-currency-response.json"
        ),
        Arguments.of(
            "employment-missing-grossRentalIncome-request.json",
            "employment-missing-grossRentalIncome-response.json"
        ),
        Arguments.of(
            "employment-missing-grossRentalIncome-and-currency-request.json",
            "employment-missing-grossRentalIncome-and-currency-response.json"
        )
    );
}




----

@ParameterizedTest
@MethodSource("invalidEmploymentRequests")
@Sql("classpath:upgrade/sql/asset-dataset.sql")
void statusEmployment_shouldReturn428_whenEmploymentIsInvalid(
        String requestJsonPath,
        String responseJsonPath
) throws Exception {

    // GIVEN
    jurisdictionGateway.setUserJurisdiction(
            TestUserJurisdiction.builder().admin().build()
    );

    String input = JsonUtils.retrieveJson(
            "json/commercial_real_estate/employment/%s".formatted(requestJsonPath)
    );

    String expectedResponse = JsonUtils.retrieveJson(
            "json/commercial_real_estate/employment/%s".formatted(responseJsonPath)
    );

    // WHEN + THEN
    mockMvc.perform(
            put("/v1/commercial-real-estate/{assetId}/employment",
                    "c50ff2d-3d8d-4231-b267-12aba9666f")
                    .contentType(MediaType.APPLICATION_JSON)
                    .content(input)
    )
    .andExpect(status().isPreconditionRequired())   // ✅ 428
    .andExpect(content().json(expectedResponse, true));
}
